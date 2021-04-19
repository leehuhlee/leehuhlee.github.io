---
layout: post
title: "[C#] Chatting Test"
date: 2021-04-16
excerpt: "Job Queue"
tags: [C#, Server]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part4/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part4: 게임 서버</a></center>

# Chatting Test

## Server

* PacketGenerator\PDL.bat
{% highlight bat %}
<?xml version="1.0" encoding="utf-8" ?>
<PDL>
  <packet name="C_Chat">
    <string name="chat"/>
  </packet>
  <packet name="S_Chat">
    <int name="playerId"/>
    <string name="chat"/>
  </packet>
</PDL>
{% endhighlight %}

* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    public static void C_ChatHandler(PacketSession session, IPacket packet)
    {
        C_Chat chatPacket = packet as C_Chat;
        ClientSession clientSession = session as ClientSession;

        if (clientSession.Room == null)
            return;

        // broadcasting with session and chatting content
        clientSession.Room.Broadcast(clientSession, chatPacket.chat);
    }
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
class ClientSession : PacketSession
{
    public int SessionId { get; set; }
    public GameRoom Room { get; set; }

    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        Program.Room.Enter(this);
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        PacketManager.Instance.OnRecvPacket(this, buffer);

    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        SessionManager.Instance.Remove(this);
        if(Room != null)
        {
            Room.Leave(this);
            Room = null;
        }

        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* Server\Session\SessionManager.cs
{% highlight C# %}
class SessionManager
{
    static SessionManager _session = new SessionManager();
    public static SessionManager Instance { get { return _session; } }

    int _sessionId = 0;

    Dictionary<int, ClientSession> _sessions = new Dictionary<int, ClientSession>();

    object _lock = new object();

    public ClientSession Generate()
    {
        lock (_lock)
        {
            int sessionId = ++_sessionId;

            ClientSession session = new ClientSession();
            session.SessionId = sessionId;
            _sessions.Add(sessionId, session);

            Console.WriteLine($"Connected: {sessionId}");

            return session;
        }
    }

    public ClientSession Find(int id)
    {
        lock (_lock)
        {
            ClientSession session = null;
            _sessions.TryGetValue(id, out session);
            return session;
        }
    }

    public void Remove(ClientSession session)
    {
        lock (_lock)
        {
            _sessions.Remove(session.SessionId);
        }
    }
}
{% endhighlight %}

* Server\GameRoom.cs
{% highlight C# %}
class GameRoom
{
    List<ClientSession> _sessions = new List<ClientSession>();
    object _lock = new object();

    // broadcast for all session in same room
    public void Broadcast(ClientSession session, string chat)
    {
        S_Chat packet = new S_Chat();
        packet.playerId = session.SessionId;
        packet.chat = chat;
        // find base interface
        ArraySegment<byte> segment = packet.Write();

        lock (_lock)
        {
            // send chat to all sessions
            foreach (ClientSession s in _sessions)
                s.Send(segment);
        }
    }

    public void Enter(ClientSession session)
    {
        lock (_lock)
        {
            _sessions.Add(session);
            session.Room = this;
        }
        
    }

    public void Leave(ClientSession session)
    {
        lock (_lock)
        {
            _sessions.Remove(session);
        }
    }
}
{% endhighlight %}

* Server\Program.cs
{% highlight C# %}
class Program
{
    static Listener _listener = new Listener();
    public static GameRoom Room= new GameRoom();

    static void Main(string[] args)
    {
        PacketManager.Instance.Register();

        string host = Dns.GetHostName();
        IPHostEntry ipHost = Dns.GetHostEntry(host);
        IPAddress ipAddr = ipHost.AddressList[0];
        IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

        // Create Session by SessionManager
        _listener.Init(endPoint, () => { return SessionManager.Instance.Generate(); });
        Console.WriteLine("Listening...");

        while (true) { }
    }
}
{% endhighlight %}

* ServerCore\Session.cs
  - update for multi thread
{% highlight C# %}
public abstract class Session
{
    ...

    // Initialize
    void Clear()
    {
        lock (_lock)
        {
            _sendQueue.Clear();
            _pendingList.Clear();
        }
    }

    ...

    public void Disconnect()
    {
        if (Interlocked.Exchange(ref _disconnected, 1) == 1)
            return;

        OnDisconnected(_socket.RemoteEndPoint);
        _socket.Shutdown(SocketShutdown.Both);
        _socket.Close();
        Clear();
    }

    #region Network Communication

    void RegisterSend()
    {
        if (_disconnected == 1)
            return;

        while (_sendQueue.Count > 0)
        {
            ArraySegment<byte> buff = _sendQueue.Dequeue();
            _pendingList.Add(buff);
        }

        _sendArgs.BufferList = _pendingList;

        try
        {
            bool pending = _socket.SendAsync(_sendArgs);
            if (pending == false)
                OnSendCompeleted(null, _sendArgs);
        }
        catch(Exception e)
        {
            Console.WriteLine($"OnSendCompleted Failed {e}");
        }
    }

    void OnSendCompeleted(object sender, SocketAsyncEventArgs args)
    {
        lock (_lock)
        {
            if (args.BytesTransferred > 0 && args.SocketError == SocketError.Success)
            {
                try
                {
                    _sendArgs.BufferList = null;
                    _pendingList.Clear();

                    OnSend(_sendArgs.BytesTransferred);
                    
                    if (_sendQueue.Count > 0)
                        RegisterSend();
                }
                catch (Exception e)
                {
                    Console.WriteLine($"OnSendCompeleted Failed {e}");
                }
            }
            else
            {
                Disconnect();
            }
        }
    }

    void RegisterRecv()
    {
        if (_disconnected == 1)
            return;

        _recvBuffer.Clean();
        ArraySegment<byte> segment = _recvBuffer.WriteSegment;
        _recvArgs.SetBuffer(segment.Array, segment.Offset, segment.Count);

        try
        {
            bool pending = _socket.ReceiveAsync(_recvArgs);
            if (pending == false)
                OnRecvCompleted(null, _recvArgs);
        }
        catch(Exception e)
        {
            Console.WriteLine($"RegisterRecv Failed {e}");
        }
    }

    ...
}
{% endhighlight %}

## Client

* DummyClient\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    public static void S_ChatHandler(PacketSession session, IPacket packet)
    {
        // send C_chat and reply by S_Chat
        S_Chat chatPacket = packet as S_Chat;
        ServerSession serverSession = session as ServerSession;

        Console.WriteLine(chatPacket.chat);
    }
}
{% endhighlight %}

* DummyClient\Program.cs
{% highlight C# %}
class Program
{
    static void Main(string[] args)
    {
        ...

        Connector connector = new Connector();

        // Set count
        connector.Connect(endPoint, () => { return SessionManager.Instance.Generate(); }, 100);

        while (true)
        {
            try
            {
                // all sessions send chatting message to server
                SessionManager.Instance.SendForEach();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.ToString());
            }

            Thread.Sleep(250);
        }
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
class ServerSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
    }

    ...

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        PacketManager.Instance.OnRecvPacket(this, buffer);
    }

    public override void OnSend(int numOfBytes){ }
}
{% endhighlight %}

* DummyClient\SessionManager.cs
{% highlight C# %}
class SessionManager
{
    static SessionManager _session = new SessionManager();
    public static SessionManager Instance { get { return _session; } }

    List<ServerSession> _sessions = new List<ServerSession>();
    // for multi thread
    object _lock = new object();

    public void SendForEach()
    {
        lock (_lock)
        {
            foreach(ServerSession session in _sessions)
            {
                C_Chat chatPacket = new C_Chat();
                chatPacket.chat = $"Hello Server!";
                ArraySegment<byte> segment = chatPacket.Write();

                session.Send(segment);
            }
        }
    }

    public ServerSession Generate()
    {
        lock (_lock)
        {
            ServerSession session = new ServerSession();
            _sessions.Add(session);
            return session;
        }

    }
}
{% endhighlight %}

* PacketGenerator\PacketFormat.cs
{% highlight C# %}
class PacketFormat
{
    // {0} Packet Assign
    public static string managerFormat =
@"using ServerCore;
using System;
using System.Collections.Generic;

class PacketManager
{
    #region Singleton
    static PacketManager _instance = new PacketManager();
    public static PacketManager Instance { get { return _instance; } }
    #endregion

    PacketManager()
    {
        Register();
    }

    Dictionary<ushort, Action<PacketSession, ArraySegment<byte>>> _onRecv = new Dictionary<ushort, Action<PacketSession, ArraySegment<byte>>>();
    Dictionary<ushort, Action<PacketSession, IPacket>> _handler = new Dictionary<ushort, Action<PacketSession, IPacket>>();

   ...
}";
}
{% endhighlight %}

* Server\GameRoom.cs
{% highlight C# %}
class GameRoom
{
    List<ClientSession> _sessions = new List<ClientSession>();
    object _lock = new object();

    public void Broadcast(ClientSession session, string chat)
    {
        S_Chat packet = new S_Chat();
        packet.playerId = session.SessionId;
        packet.chat = $"{chat} I am {packet.playerId}";
        ArraySegment<byte> segment = packet.Write();

        // O(N²)
        lock (_lock)
        {
            foreach (ClientSession s in _sessions)
                s.Send(segment);
        }
    }
}
{% endhighlight %}

* Server\Program.cs
{% highlight C# %}
class Program
{
    ...
    static void Main(string[] args)
    {
        // remove Register() for automatic
        string host = Dns.GetHostName();
        ...
    }
}
{% endhighlight %}

* ServerCore\Connector.cs
{% highlight C# %}
public class Connector
{
   ...

    // the initial count value is 1, but you can set the connector count
    public void Connect(IPEndPoint endPoint, Func<Session> sessionFactory, int count = 1)
    {
        for(int i=0; i<count; i++)
        {
            Socket socket = new Socket(endPoint.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
            _sessionFactory = sessionFactory;

            SocketAsyncEventArgs args = new SocketAsyncEventArgs();
            args.Completed += OnConnectCompleted;
            args.RemoteEndPoint = endPoint;
            args.UserToken = socket;

            RegisterConnect(args);
        }
    }
}
{% endhighlight %}

### Test

<figure class="half">
  <a href="/assets/img/posts/cshap_chatting_test/0.jpg"><img src="/assets/img/posts/cshap_chatting_test/0.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/1.jpg"><img src="/assets/img/posts/cshap_chatting_test/1.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

  - but, this lock machanism is not good
  - all threads is locked except main thread, so they hier new thread again and again

<figure>
  <a href="/assets/img/posts/cshap_chatting_test/2.jpg"><img src="/assets/img/posts/cshap_chatting_test/2.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

## Job Queue

* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    public static void C_ChatHandler(PacketSession session, IPacket packet)
    {
        ...
        if (clientSession.Room == null)
            return;
            
        // this room is for null reference exception
        GameRoom room = clientSession.Room;
        // push the action
        room.Push(() => room.Broadcast(clientSession, chatPacket.chat));
    }
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
class ClientSession : PacketSession
{
    ...

    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        // push the action
        // they do, when they can do
        Program.Room.Push(() => Program.Room.Enter(this));
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        PacketManager.Instance.OnRecvPacket(this, buffer);
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        SessionManager.Instance.Remove(this);
        if(Room != null)
        {
            GameRoom room = Room;
            room.Push(() => room.Leave(this));
            Room = null;
        }

        Console.WriteLine($"OnDisconnected: {endPoint}");
    }
{% endhighlight %}

* Server\GameRoom.cs
{% highlight C# %}
class GameRoom : IJobQueue
{
    List<ClientSession> _sessions = new List<ClientSession>();
    // remove lock because JobQueue has lock
    JobQueue _jobQueue = new JobQueue();

    public void Push(Action job)
    {
        _jobQueue.Push(job);
    }

    public void Broadcast(ClientSession session, string chat)
    {
        S_Chat packet = new S_Chat();
        packet.playerId = session.SessionId;
        packet.chat = $"{chat} I am {packet.playerId}";
        ArraySegment<byte> segment = packet.Write();

        foreach (ClientSession s in _sessions)
            s.Send(segment);
    }

    public void Enter(ClientSession session)
    {
        _sessions.Add(session);
        session.Room = this;
    }

    public void Leave(ClientSession session)
    {
        _sessions.Remove(session);
    }
}
{% endhighlight %}

* ServerCore\JobQueue.cs
{% highlight C# %}
public interface IJobQueue
{
    void Push(Action job);
}

public class JobQueue : IJobQueue
{
    Queue<Action> _jobQueue = new Queue<Action>();
    // for multi thread
    object _lock = new object();
    // controle queue that stock in JobQueue to start
    bool _flush = false;

    public void Push(Action job)
    {
        bool flush = false;
        lock (_lock)
        {
            _jobQueue.Enqueue(job);
            if (_flush == false)
                flush = _flush = true;
        }

        if (flush)
            Flush();
    }

    // do action;
    void Flush()
    {
        while (true)
        {
            // use lock when pop is excuted
            // because other thread can use push and they push job in JobQueue
            Action action = Pop();
            if (action == null)
                return;

            action.Invoke();
        }
    }

    Action Pop()
    {
        lock (_lock)
        {
            if (_jobQueue.Count == 0)
            {
                // for next thread
                _flush = false;
                return null;
            }

            return _jobQueue.Dequeue();
        }
    }

}
{% endhighlight %}

### Test

<figure class="third">
  <a href="/assets/img/posts/cshap_chatting_test/3.jpg"><img src="/assets/img/posts/cshap_chatting_test/3.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/4.jpg"><img src="/assets/img/posts/cshap_chatting_test/4.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/5.jpg"><img src="/assets/img/posts/cshap_chatting_test/5.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
