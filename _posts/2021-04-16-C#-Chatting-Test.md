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

        while (true)
        {

        }
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



[Download](https://github.com/leehuhlee/CShap){: .btn}
