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

# Packet Merge-Sending

* DummyClient\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    public static void S_ChatHandler(PacketSession session, IPacket packet)
    {
        S_Chat chatPacket = packet as S_Chat;
        ServerSession serverSession = session as ServerSession;
    }
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
...
// remove log
public override void OnSend(int numOfBytes){ }
{% endhighlight %}

* Server\GameRoom.cs
{% highlight C# %}
class GameRoom : IJobQueue
{
    List<ClientSession> _sessions = new List<ClientSession>();
    JobQueue _jobQueue = new JobQueue();
    // ArraySegment is content of packet(byte)
    List<ArraySegment<byte>> _pendingList = new List<ArraySegment<byte>>();

    public void Push(Action job)
    {
        _jobQueue.Push(job);
    }

    // Send and Clear
    // you don't need lock because of JobQueue
    public void Flush()
    {
        foreach (ClientSession s in _sessions)
            s.Send(_pendingList);

        Console.WriteLine($"Flushed {_pendingList.Count} items");
        _pendingList.Clear();
    }

    public void Broadcast(ClientSession session, string chat)
    {
        S_Chat packet = new S_Chat();
        packet.playerId = session.SessionId;
        packet.chat = $"{chat} I am {packet.playerId}";
        ArraySegment<byte> segment = packet.Write();

        // Merge
        _pendingList.Add(segment);
    }
    ...
}
{% endhighlight %}



* Server\Program.cs
{% highlight C# %}
class Program
{
    ...

    static void Main(string[] args)
    {
       ...

        while (true)
        {
            Room.Push(() => Room.Flush());
            Thread.Sleep(250);
        }
    }
{% endhighlight %}

* ServerCore\Listener.cs
{% highlight C# %}
public class Listener
{
    ...

    public void Init(IPEndPoint endPoint, Func<Session> sessionFactory, int register = 10, int backlog = 100)
    {
        ...

        // backlog: maximal waiting count
        _listenSocket.Listen(backlog);

        for (int i = 0; i < register; i++)
        {
            SocketAsyncEventArgs args = new SocketAsyncEventArgs();
            args.Completed += new EventHandler<SocketAsyncEventArgs>(OnAcceptCompleted);
            RegisterAccept(args);
        }
    }
{% endhighlight %}

* ServerCore\SendBuffer.cs
{% highlight C# %}
public class SendBufferHelper
{
    public static ThreadLocal<SendBuffer> CurrentBuffer = new ThreadLocal<SendBuffer>(() => { return null; });

    // change buffer size
    public static int ChunkSize { get; set; } = 65535 * 100;
    ...
}
{% endhighlight %}

* ServerCore\Session.cs
{% highlight C# %}
public abstract class PacketSession: Session
{
    public static readonly int HeaderSize = 2;

    public sealed override int OnRecv(ArraySegment<byte> buffer)
    {
        int processLen = 0;
        // check Packet Count
        int packetCount = 0;

        while (true)
        {
            if (buffer.Count < HeaderSize)
                break;

            ushort dataSize = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
            if (buffer.Count < dataSize)
                break;

            OnRecvPacket(new ArraySegment<byte>(buffer.Array, buffer.Offset, dataSize));
            packetCount++;

            processLen += dataSize;
            buffer = new ArraySegment<byte>(buffer.Array, buffer.Offset + dataSize, buffer.Count - dataSize);
        }

        if(packetCount >1)
            Console.WriteLine($"Packet Merge-Sending : {packetCount}");

        return processLen;
    }

    public abstract void OnRecvPacket(ArraySegment<byte> buffer);

}

public abstract class Session
{
    ...

    // change buffer size
    RecvBuffer _recvBuffer = new RecvBuffer(65535);

    object _lock = new object();
    Queue<ArraySegment<byte>> _sendQueue = new Queue<ArraySegment<byte>>();
    List<ArraySegment<byte>> _pendingList = new List<ArraySegment<byte>>();

    ...

    public void Send(List<ArraySegment<byte>> sendBuffList)
    {
        if(sendBuffList.Count == 0)
            return;

        lock (_lock)
        {
            foreach(ArraySegment<byte> sendBuff in sendBuffList)
                _sendQueue.Enqueue(sendBuff);

            if (_pendingList.Count == 0)
                RegisterSend();
        }
    }
    ...
}
{% endhighlight %}

### Test

<figure class="half">
  <a href="/assets/img/posts/cshap_chatting_test/6.jpg"><img src="/assets/img/posts/cshap_chatting_test/6.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/7.jpg"><img src="/assets/img/posts/cshap_chatting_test/7.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

# Job Timer

* Server\JobTimer.cs
{% highlight C# %}
// JobTimerElem will be used in PriorityQueue, so it should support Icomparable Interface
struct JobTimerElem : IComparable<JobTimerElem>
{
    // excute Time
    public int execTick; 
    public Action action;

    public int CompareTo(JobTimerElem other)
    {
        // return smaller
        return other.execTick - execTick;
    }
}

class JobTimer
{
    PriorityQueue<JobTimerElem> _pq = new PriorityQueue<JobTimerElem>();
    // for multi thread
    object _lock = new object();

    public static JobTimer Instance { get; } = new JobTimer();

    public void Push(Action action, int tickAfter = 0) 
    {
        JobTimerElem job;
        job.execTick = System.Environment.TickCount + tickAfter;
        job.action = action;

        lock (_lock)
        {
            _pq.Push(job);
        }
    }

    public void Flush()
    {
        while (true)
        {
            int now = System.Environment.TickCount;

            JobTimerElem job;

            lock (_lock)
            {
                if (_pq.Count == 0)
                    break;

                job = _pq.Peek();
                // this job is not for now
                if (job.execTick > now)
                    break;

                _pq.Pop();
            }

            job.action.Invoke();
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

    static void FlushRoom()
    {
        Room.Push(() => Room.Flush());
        // for next job
        JobTimer.Instance.Push(FlushRoom, 250);
    }

    static void Main(string[] args)
    {
        ...
        Console.WriteLine("Listening...");

        // excute directly
        JobTimer.Instance.Push(FlushRoom);
        // Use PriorityQueue as reservation system
        // Fist in First Out
        while (true)
        {
            JobTimer.Instance.Flush();
        }
    }
}
{% endhighlight %}

* ServerCore\PriorityQueue.cs
  - PriorityQueue is on <a href="https://leehuhlee.github.io/C-AStar-Maze/">A* Maze</a> Post
{% highlight C# %}
public class PriorityQueue<T> where T : IComparable<T>
{
    List<T> _heap = new List<T>();

    public int Count { get { return _heap.Count; } }

    // O(logN)
    public void Push(T data)
    {
        _heap.Add(data);

        int now = _heap.Count - 1;
        while (now > 0)
        {
            int next = (now - 1) / 2;
            if (_heap[now].CompareTo(_heap[next]) < 0)
                break; // 실패

            T temp = _heap[now];
            _heap[now] = _heap[next];
            _heap[next] = temp;

            now = next;
        }
    }

    // O(logN)
    public T Pop()
    {
        T ret = _heap[0];

        int lastIndex = _heap.Count - 1;
        _heap[0] = _heap[lastIndex];
        _heap.RemoveAt(lastIndex);
        lastIndex--;

        int now = 0;
        while (true)
        {
            int left = 2 * now + 1;
            int right = 2 * now + 2;

            int next = now;
            if (left <= lastIndex && _heap[next].CompareTo(_heap[left]) < 0)
                next = left;
            if (right <= lastIndex && _heap[next].CompareTo(_heap[right]) < 0)
                next = right;

            if (next == now)
                break;

            T temp = _heap[now];
            _heap[now] = _heap[next];
            _heap[next] = temp;
            now = next;
        }

        return ret;
    }

    // not remove just read
    // return oldest
    public T Peek()
    {
        if (_heap.Count == 0)
            return default(T);
        return _heap[0];
    }
}
{% endhighlight %}

### Test

<figure class="half">
  <a href="/assets/img/posts/cshap_chatting_test/8.jpg"><img src="/assets/img/posts/cshap_chatting_test/8.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/9.jpg"><img src="/assets/img/posts/cshap_chatting_test/9.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

# Unity

## System Link

* PacketGenerator\PacketFormat.cs
  - `Span` and `TryWriteBytes` is not applied in Unity

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

    public void Register()
    {
{0}
    }

    public void OnRecvPacket(PacketSession session, ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        Action<PacketSession, ArraySegment<byte>> action = null;
        if (_onRecv.TryGetValue(id, out action))
            action.Invoke(session, buffer);
    }

    void MakePacket<T>(PacketSession session, ArraySegment<byte> buffer) where T: IPacket, new()
    {
        T pkt = new T();
        pkt.Read(buffer);

        Action<PacketSession, IPacket> action = null;
        if (_handler.TryGetValue(pkt.Protocol, out action))
            action.Invoke(session, pkt);
    }
}";

    // {0} Packet Name
    public static string managerRegisterFormat =
@"        _onRecv.Add((ushort)PacketID.{0}, MakePacket<{0}>);
        _handler.Add((ushort)PacketID.{0}, PacketHandler.{0}Handler);";

    // {0} Packet Name/Number List
    // {1} Packet List
    public static string fileFormat =
@"using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using ServerCore;

public enum PacketID
{
    {0}
}

interface IPacket
{
    ushort Protocol { get; }
    void Read(ArraySegment<byte> segment);
    ArraySegment<byte> Write();
}


{1}";

    // {0} Packet Name
    // {1} Packet Number
    public static string packetEnumFormat = 
@"{0} = {1},";


    // {0} Packet Name
    // {1} Member Variable
    // {2} Member Variable Read
    // {3} Member Variable Write
    public static string packetFormat =
@"
class {0} : IPacket
{
    {1}   

    public ushort Protocol { get { return (ushort)PacketID.{0}; } }
    
    public void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;
        count += sizeof(ushort);
        count += sizeof(ushort);
        {2}
    }

    public ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);
        ushort count = 0;
        count += sizeof(ushort);
        Array.Copy(BitConverter.GetBytes((ushort)PacketID.{0}), 0, segment.Array, segment.Offset + count, sizeof(ushort));
        count += sizeof(ushort);
        {3}
        Array.Copy(BitConverter.GetBytes(count), 0, segment.Array, segment.Offset, sizeof(ushort));
        return SendBufferHelper.Close(count);
    }
}";

    // {0} Variable Type
    // {1} Variable Name
    public static string memberFormat = 
@"public {0} {1};";

    // {0} List Name[Capital]
    // {1} List Name[small]
    // {2} Member Variables
    // {3} Member Variables Read
    // {4} Member Variables Write
    public static string memberListFormat =
@"
public class {0}
{
    {2}

    public void Read(ArraySegment<byte> segment, ref ushort count)
    {
        {3}
    }

    public bool Write(ArraySegment<byte> segment, ref ushort count)
    {
        bool success = true;
        {4}
        return success;
    }
}

public List<{0}> {1}s = new List<{0}>();";

    // {0} Variable Name
    // {1} To~ Variable Type
    // {2} Vatiable Type
    public static string readFormat =
@"this.{0} = BitConverter.{1}(segment.Array, segment.Offset + count);
count += sizeof({2});";

    // {0} Variable Name
    // {1} Variable Type
    public static string readByteFormat =
@"this.{0} = ({1})segment.Array[segment.Offset + count];
count += sizeof({1});";

    // {0} Variable Name
    public static string readStringFormat =
@"ushort {0}Len = BitConverter.ToUInt16(segment.Array, segment.Offset + count);
count += sizeof(ushort);
this.{0} = Encoding.Unicode.GetString(segment.Array, segment.Offset + count, {0}Len);
count += {0}Len;";

    // {0} List Name[Capital]
    // {1} List Name[small]
    public static string readListFormat =
@"this.{1}s.Clear();
ushort {1}Len = BitConverter.ToUInt16(segment.Array, segment.Offset + count);
count += sizeof(ushort);
for(int i=0; i<{1}Len; i++)
{
    {0} {1} = new {0}();
    {1}.Read(s, ref count);
    {1}s.Add({1});
}";

    // {0} Variable Name
    // {1} Variable Type
    public static string writeFormat =
 @"Array.Copy(BitConverter.GetBytes(this.{0}), 0, segment.Array, segment.Offset + count, sizeof({1}));
count += sizeof({1});";

    // {0} Variable Name
    // {1} Variable Type
    public static string writeByteFormat =
@"segment.Array[segment.Offset + count] = ({1})this.{0} ;
count += sizeof({1});";

    // {0} Variable Name
    public static string writeStringFormat =
@"ushort {0}Len = (ushort)Encoding.Unicode.GetBytes(this.{0}, 0, this.{0}.Length, segment.Array, segment.Offset + count + sizeof(ushort));
Array.Copy(BitConverter.GetBytes({0}Len), 0, segment.Array, segment.Offset + count, sizeof(ushort));
count += sizeof(ushort);
count += {0}Len;";

    // {0} List Name[Capital]
    // {1} List Name[small]
    public static string writeListFormat =
@"Array.Copy(BitConverter.GetBytes((ushort)this.{1}s.Count), 0, segment.Array, segment.Offset + count, sizeof(ushort));
count += sizeof(ushort);
foreach({0} {1} in {1}s)
    {1}.Write(segment, ref count);";
}
{% endhighlight %}

* DummyClient\Program.cs
  - for Test, change connect count to 10
{% highlight C# %}
class Program
{
    static void Main(string[] args)
    {
        ...
        connector.Connect(endPoint, () => { return SessionManager.Instance.Generate(); }, 10);
        ...
    }
}
{% endhighlight %}

## Unity
  - copy files from `DummyClient/Packet` in `Server` solution
  - copy files from `ServerCore` in `Server` solution and create `Network` folder

* Network\SendBuffer.cs
{% highlight C# %}
public ArraySegment<byte> Open(int reserveSize)
{
    // remove null check
    return new ArraySegment<byte>(_buffer, _usedSize, reserveSize);
}
{% endhighlight %}

* NetworkManager.cs
  - copy codes from `DummyClient\Program.cs` in `Server` solution

{% highlight C# %}
public class NetworkManager : MonoBehaviour
{
    // connect count is only 1
    ServerSession _session = new ServerSession();

    void Start()
    {
        string host = Dns.GetHostName();
        IPHostEntry ipHost = Dns.GetHostEntry(host);
        IPAddress ipAddr = ipHost.AddressList[0];
        IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

        Connector connector = new Connector();

        connector.Connect(endPoint, () => { return _session; }, 1);
    }
}
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/cshap_chatting_test/11.jpg"><img src="/assets/img/posts/cshap_chatting_test/11.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/12.jpg"><img src="/assets/img/posts/cshap_chatting_test/12.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/13.jpg"><img src="/assets/img/posts/cshap_chatting_test/13.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

### Test

<figure>
  <a href="/assets/img/posts/cshap_chatting_test/10.jpg"><img src="/assets/img/posts/cshap_chatting_test/10.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

## Player
  - Create Cylinder in Unity and change name to `Player`

<figure>
  <a href="/assets/img/posts/cshap_chatting_test/15.jpg"><img src="/assets/img/posts/cshap_chatting_test/15.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

* Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_ChatHandler(PacketSession session, IPacket packet)
	{
		S_Chat chatPacket = packet as S_Chat;
		ServerSession serverSession = session as ServerSession;

        {
			Debug.Log(chatPacket.chat);

            // find game object and print
			GameObject go = GameObject.Find("Player");
			if (go == null)
				Debug.Log("Player not found");
			else
				Debug.Log("Player found");
		}
	}
}
{% endhighlight %}
  - when main thread is busy, then the job is continue excuted on other thread
  - but in Unity, other thread cannot be accessed on part which related with Game

* Assets\Scripts\PacketQueue.cs
  - so make packet, and save this packet on queue
  - and when unity main thread is working status, excute 
{% highlight C# %}
public class PacketQueue
{
    // for free access
    public static PacketQueue Instance { get; } = new PacketQueue();

    Queue<IPacket> _packetQueue = new Queue<IPacket>();
    // for multi Thread
    object _lock = new object();

    // packet push
    public void Push(IPacket packet)
    {
        lock (_lock)
        {
            _packetQueue.Enqueue(packet);
        }
    }

    // packet pop
    public IPacket Pop()
    {
        lock (_lock)
        {
            if (_packetQueue.Count == 0)
                return null;

            return _packetQueue.Dequeue();
        }
    }
}
{% endhighlight %}

* Assets\Scripts\NetworkManager.cs
{% highlight C# %}
public class NetworkManager : MonoBehaviour
{
    ServerSession _session = new ServerSession();

    void Start()
    {
        string host = Dns.GetHostName();
        IPHostEntry ipHost = Dns.GetHostEntry(host);
        IPAddress ipAddr = ipHost.AddressList[0];
        IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

        Connector connector = new Connector();

        connector.Connect(endPoint,
            () => { return _session; },
                    1);

        StartCoroutine("CoSendPacket");
    }

    void Update()
    {
        IPacket packet = PacketQueue.Instance.Pop();
        if (packet != null)
        {
            PacketManager.Instance.HandlePacket(_session, packet);
        }
    }

    IEnumerator CoSendPacket()
    {
        while(true)
        {
            yield return new WaitForSeconds(3.0f);

            C_Chat chatPacket = new C_Chat();
            chatPacket.chat = "Hello Unity!";
            ArraySegment<byte> segment = chatPacket.Write();

            _session.Send(segment);
        }
    }
}
{% endhighlight %}
  
  - Create empty game object and change name to `NetworkManager`
  - drag and drop `NetworkManager.cs` to `NetworkManager`

<figure>
  <a href="/assets/img/posts/cshap_chatting_test/20.jpg"><img src="/assets/img/posts/cshap_chatting_test/20.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>
  
* PacketGenerator\ClientPacketManager.cs
  - for automatic, you should call `PacketHandler` from `ClientPacketManager`
  - for automatic, you should edit `PacketFormet` to edit `ClientPacketManager` from `Server` solution

{% highlight C# %}
class PacketFormat
{
    // {0} Packet Asign
    public static string managerFormat =
@"using ServerCore;
...

class PacketManager
{
	...

    // Func is delegate
    // Func is already defined type
	Dictionary<ushort, Func<PacketSession, ArraySegment<byte>, IPacket>> _makeFunc = new Dictionary<ushort, Func<PacketSession, ArraySegment<byte>, IPacket>>();
	Dictionary<ushort, Action<PacketSession, IPacket>> _handler = new Dictionary<ushort, Action<PacketSession, IPacket>>();
		
...

    // onRecvCallback is an option about how to process
	public void OnRecvPacket(PacketSession session, ArraySegment<byte> buffer, Action<PacketSession, IPacket> onRecvCallback = null)
	{
		...
		ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
		count += 2;

		Func<PacketSession, ArraySegment<byte>, IPacket> func = null;
		if (_makeFunc.TryGetValue(id, out func))
        {
            // make packet
			IPacket packet = func.Invoke(session, buffer);

            // for other job
			if (onRecvCallback != null)
				onRecvCallback.Invoke(session, packet);
            // call handler
			else
				HandlePacket(session, packet);
		}
	}

    // separate make packet and call handler
	T MakePacket<T>(PacketSession session, ArraySegment<byte> buffer) where T : IPacket, new()
	{
		T pkt = new T();
		pkt.Read(buffer);
		return pkt;
	}

	public void HandlePacket(PacketSession session, IPacket packet)
	{
		Action<PacketSession, IPacket> action = null;
		if (_handler.TryGetValue(packet.Protocol, out action))
			action.Invoke(session, packet);
	}
}";

    // {0} Packet Name
    public static string managerRegisterFormat =
@"		_makeFunc.Add((ushort)PacketID.{0}, MakePacket<{0}>);
		_handler.Add((ushort)PacketID.{0}, PacketHandler.{0}Handler);";

...

public interface IPacket
{
	...
}
...
";
...
}
{% endhighlight %}

* Assets\Scripts\Network\ServerSession.cs
  - next step of `OnRecvPacket`

{% highlight C# %}
class ServerSession : PacketSession
{
    ...
    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        // push packet on PacketQueue
        PacketManager.Instance.OnRecvPacket(this, buffer, (s, p) => PacketQueue.Instance.Push(p));
    }
    ...
}
{% endhighlight %}

* NetworkManager.cs
{% highlight C# %}
public class NetworkManager : MonoBehaviour
{
    ...

    void Start()
    {
        ...

        Connector connector = new Connector();

        connector.Connect(endPoint,
            () => { return _session; },
                    1);

        StartCoroutine("CoSendPacket");
    }

    // call pop and handler
    void Update()
    {
        IPacket packet = PacketQueue.Instance.Pop();
        if (packet != null)
        {
            PacketManager.Instance.HandlePacket(_session, packet);
        }
    }

    // for send
    IEnumerator CoSendPacket()
    {
        while(true)
        {
            // per 3 sec
            yield return new WaitForSeconds(3.0f);

            // for send packet
            C_Chat chatPacket = new C_Chat();
            chatPacket.chat = "Hello Unity!";
            // extract to ArraySegment<byte>
            // write on send buffer
            ArraySegment<byte> segment = chatPacket.Write();

            _session.Send(segment);
        }
    }
}
{% endhighlight %}

* Assets\Scripts\Network\SendBuffer.cs
{% highlight C# %}
public class SendBufferHelper
{
    ...
    public static int ChunkSize { get; set; } = 65535;
    ...
}

...
{% endhighlight %}

* NetworkManager.cs
{% highlight C# %}

{% endhighlight %}


### Test
  - execute `Server` from `Server` solution
  - execute Unity
  
<figure>
  <a href="/assets/img/posts/cshap_chatting_test/14.jpg"><img src="/assets/img/posts/cshap_chatting_test/14.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

## Server

* PacketGenerator\PDL.xml
  - we used `S_Chat` and `C_Chat`  for just chatting message
  - now, we will change for player(Enter, Leave and Move)
{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<PDL>
  <packet name="S_BroadcastEnterGame">
    <int name="playerId"/>
    <float name="posX"/>
    <float name="posY"/>
    <float name="posZ"/>
  </packet>
  <packet name="C_LeaveGame">
  </packet>
  <packet name="S_BroadcastLeaveGame">
    <int name="playerId"/>
  </packet>
  <packet name="S_PlayerList">
    <list name="player">
      <bool name="isSelf"/>
      <int name="playerId"/>
      <float name="posX"/>
      <float name="posY"/>
      <float name="posZ"/>
    </list>
  </packet>
  <packet name="C_Move">
    <float name="posX"/>
    <float name="posY"/>
    <float name="posZ"/>
  </packet>
  <packet name="S_BroadcastMove">
    <int name="playerId"/>
    <float name="posX"/>
    <float name="posY"/>
    <float name="posZ"/>
  </packet>
</PDL>
{% endhighlight %}

  - `S_BroadcastEnterGame` is for broadcasting new player info to all players
  - `C_LeaveGame` is for getting leave claim from client
  - `S_BroadcastLeaveGame` is for broadcasting leaving player info to all players
  - `S_PlayerList` is for sending all players info to new player and `isSelf` is for checking it is playerself
  - `C_Move` is for getting move claim from client and it dosen't have playerId because it has already client session
  - `S_BroadcastMove` is for broadcasting player move to all players

* PacketGenerator\PacketFormat.cs
{% highlight C# %}
class PacketFormat
{
    // {0} Packet Asign
    public static string managerFormat =
@"using ServerCore;
using System;
using System.Collections.Generic;

public class PacketManager
...
":

    // {0} Packet Name
    // {1} Member Variable
    // {2} Member Variable Read
    // {3} Member Variable Write
    public static string packetFormat =
@"

public class {0} : IPacket
...
";

...
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
class ClientSession : PacketSession
{
    ...
    public float PosX { get; set; }
    public float PosY { get; set; }
    public float PosZ { get; set; }
    ...
}
{% endhighlight %}

* Server\GameRoom.cs
{% highlight C# %}
class GameRoom : IJobQueue
{
    ...

    public void Flush()
    {
        // N ^ 2
        foreach (ClientSession s in _sessions)
            s.Send(_pendingList);
        // remove print

        _pendingList.Clear();
    }

    // broadcasting when it flush
    public void Broadcast(ArraySegment<byte> segment)
    {
        _pendingList.Add(segment);			
    }

    public void Enter(ClientSession session)
    {
        // add player
        _sessions.Add(session);
        session.Room = this;

        // send all player list to new player
        S_PlayerList players = new S_PlayerList();
        foreach (ClientSession s in _sessions)
        {
            players.players.Add(new S_PlayerList.Player()
            {
                isSelf = (s == session),
                playerId = s.SessionId,
                posX = s.PosX,
                posY = s.PosY,
                posZ = s.PosZ
            });
        }

        session.Send(players.Write());

        // send new layer info to all player
        S_BroadcastEnterGame enter = new S_BroadcastEnterGame();
        enter.playerId = session.SessionId;
        enter.posX = 0;
        enter.posY = 0;
        enter.posZ = 0;
        Broadcast(enter.Write());
    }

    public void Leave(ClientSession session)
    {
        // remove player
        _sessions.Remove(session);

        // broadcast to all
        S_BroadcastLeaveGame leave = new S_BroadcastLeaveGame();
        leave.playerId = session.SessionId;
        Broadcast(leave.Write());
    }

    public void Move(ClientSession session, C_Move packet)
    {
        // change position
        session.PosX = packet.posX;
        session.PosY = packet.posY;
        session.PosZ = packet.posZ;

        // broadcast to all
        S_BroadcastMove move = new S_BroadcastMove();
        move.playerId = session.SessionId;
        move.posX = session.PosX;
        move.posY = session.PosY;
        move.posZ = session.PosZ;
        Broadcast(move.Write());
    }
}
{% endhighlight %}

* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void C_LeaveGameHandler(PacketSession session, IPacket packet)
	{
		ClientSession clientSession = session as ClientSession;

		if (clientSession.Room == null)
			return;

		GameRoom room = clientSession.Room;
		room.Push(() => room.Leave(clientSession));
	}

	public static void C_MoveHandler(PacketSession session, IPacket packet)
	{
		C_Move movePacket = packet as C_Move;
		ClientSession clientSession = session as ClientSession;

		if (clientSession.Room == null)
			return;

        Console.WriteLine($"{movePacket.posX}, {movePacket.posY}, {movePacket.posZ}");

		GameRoom room = clientSession.Room;
		room.Push(() => room.Move(clientSession, movePacket));
	}
}
{% endhighlight %}

* DummyClient\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_BroadcastEnterGameHandler(PacketSession session, IPacket packet)
	{
		S_BroadcastEnterGame pkt = packet as S_BroadcastEnterGame;
		ServerSession serverSession = session as ServerSession;
	}

	public static void S_BroadcastLeaveGameHandler(PacketSession session, IPacket packet)
	{
		S_BroadcastLeaveGame pkt = packet as S_BroadcastLeaveGame;
		ServerSession serverSession = session as ServerSession;
	}

	public static void S_PlayerListHandler(PacketSession session, IPacket packet)
	{
		S_PlayerList pkt = packet as S_PlayerList;
		ServerSession serverSession = session as ServerSession;
	}

	public static void S_BroadcastMoveHandler(PacketSession session, IPacket packet)
	{
		S_BroadcastMove pkt = packet as S_BroadcastMove;
		ServerSession serverSession = session as ServerSession;
	}
}
{% endhighlight %}

* DummyClient\SessionManager.cs
{% highlight C# %}
class SessionManager
{
    static SessionManager _session = new SessionManager();
    public static SessionManager Instance { get { return _session; } }

    List<ServerSession> _sessions = new List<ServerSession>();
    object _lock = new object();
    Random _rand = new Random();

    public void SendForEach()
    {
        lock (_lock)
        {
            foreach (ServerSession session in _sessions)
            {
                C_Move movePacket = new C_Move();
                movePacket.posX = _rand.Next(-50, 50);
                movePacket.posY = 0;
                movePacket.posZ = _rand.Next(-50, 50); 

                session.Send(movePacket.Write());
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

### Test
  - execute `Server` anc `DummyClient` from `Server` solution

<figure class="half">
  <a href="/assets/img/posts/cshap_chatting_test/16.jpg"><img src="/assets/img/posts/cshap_chatting_test/16.jpg"></a>
  <a href="/assets/img/posts/cshap_chatting_test/17.jpg"><img src="/assets/img/posts/cshap_chatting_test/17.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

## Client

* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	...

	public static void C_MoveHandler(PacketSession session, IPacket packet)
	{
		...

		if (clientSession.Room == null)
			return;

        // remove print

		GameRoom room = clientSession.Room;
		room.Push(() => room.Move(clientSession, movePacket));
	}
}
{% endhighlight %}

* Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_BroadcastEnterGameHandler(PacketSession session, IPacket packet)
	{
		S_BroadcastEnterGame pkt = packet as S_BroadcastEnterGame;
		ServerSession serverSession = session as ServerSession;

		PlayerManager.Instance.EnterGame(pkt);
	}

	public static void S_BroadcastLeaveGameHandler(PacketSession session, IPacket packet)
	{
		S_BroadcastLeaveGame pkt = packet as S_BroadcastLeaveGame;
		ServerSession serverSession = session as ServerSession;

		PlayerManager.Instance.LeaveGame(pkt);
	}

	public static void S_PlayerListHandler(PacketSession session, IPacket packet)
	{
		S_PlayerList pkt = packet as S_PlayerList;
		ServerSession serverSession = session as ServerSession;

		PlayerManager.Instance.Add(pkt);
	}

	public static void S_BroadcastMoveHandler(PacketSession session, IPacket packet)
	{
		S_BroadcastMove pkt = packet as S_BroadcastMove;
		ServerSession serverSession = session as ServerSession;

		PlayerManager.Instance.Move(pkt);
	}
}
{% endhighlight %}

* Unity UI
  - Create Plane and resize to (10, 10, 10) and set position to (0, 0, 0)
  - Create Material in Assets and change name to `Floor` and Albedo(color) to blue

<figure>
  <a href="/assets/img/posts/cshap_chatting_test/21.jpg"><img src="/assets/img/posts/cshap_chatting_test/21.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>

* Assets\Scripts\Player.cs
  - this is for normal player
{% highlight C# %}
public class Player : MonoBehaviour
{
    public int PlayerId { get; set; }
}
{% endhighlight %}

* Assets\Scripts\MyPlayer.cs
  - this is for player you have
{% highlight C# %}
public class MyPlayer : Player
{
    NetworkManager _network;

    void Start()
    {
        StartCoroutine("CoSendPacket");
        _network = GameObject.Find("NetworkManager").GetComponent<NetworkManager>();
    }

    // for move
    IEnumerator CoSendPacket()
    {
        while (true)
        {
            yield return new WaitForSeconds(0.25f);

            C_Move movePacket = new C_Move();
            movePacket.posX = UnityEngine.Random.Range(-50, 50);
            movePacket.posY = 0;
            movePacket.posZ = UnityEngine.Random.Range(-50, 50);

            _network.Send(movePacket.Write());
        }
    }
}
{% endhighlight %}

* Assets\Scripts\NetworkManager.cs
{% highlight C# %}
public class NetworkManager : MonoBehaviour
{
    ...

    void Update()
    {
        List<IPacket> list = PacketQueue.Instance.PopAll();
        foreach (IPacket packet in list)
            PacketManager.Instance.HandlePacket(_session, packet);
    }
}
{% endhighlight %}

* Assets\Scripts\PlayerManager.cs

  - Create `Player` prefab
<figure>
  <a href="/assets/img/posts/cshap_chatting_test/19.jpg"><img src="/assets/img/posts/cshap_chatting_test/19.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>


{% highlight C# %}
public class PlayerManager
{
    MyPlayer _myPlayer;
    Dictionary<int, Player> _players = new Dictionary<int, Player>();

    public static PlayerManager Instance { get; } = new PlayerManager();

    public void Add(S_PlayerList packet)
    {
        Object obj = Resources.Load("Player");

        foreach(S_PlayerList.Player p in packet.players)
        {
            GameObject go = Object.Instantiate(obj) as GameObject;

            // check this is my player
            if (p.isSelf)
            {
                MyPlayer myPlayer = go.AddComponent<MyPlayer>();
                myPlayer.PlayerId = p.playerId;
                myPlayer.transform.position = new Vector3(p.posX, p.posY, p.posZ);
                _myPlayer = myPlayer;
            }
            else
            {
                Player player = go.AddComponent<MyPlayer>();
                player.PlayerId = p.playerId;
                player.transform.position = new Vector3(p.posX, p.posY, p.posZ);
                _players.Add(p.playerId, player);
            }
        }
    }

    public void Move(S_BroadcastMove packet)
    {
        // check this is my player
        if (_myPlayer.PlayerId == packet.playerId)
        {
            _myPlayer.transform.position = new Vector3(packet.posX, packet.posY, packet.posZ);
        }
        else
        {
            Player player = null;
            if(_players.TryGetValue(packet.playerId, out player))
            {
                player.transform.position = new Vector3(packet.posX, packet.posY, packet.posZ);
            }
        }
    }

    public void EnterGame(S_BroadcastEnterGame packet)
    {
        if (_myPlayer.PlayerId == packet.playerId)
            return;

        Object obj = Resources.Load("Player");
        GameObject go = Object.Instantiate(obj) as GameObject;

        Player player = go.AddComponent<MyPlayer>();
        player.transform.position = new Vector3(packet.posX, packet.posY, packet.posZ);
        _players.Add(packet.playerId, player);
    }

    public void LeaveGame(S_BroadcastLeaveGame packet)
    {
        if(_myPlayer.PlayerId == packet.playerId)
        {
            GameObject.Destroy(_myPlayer.gameObject);
            _myPlayer = null;
        }
        else
        {
            Player player = null;
            if(_players.TryGetValue(packet.playerId, out player))
            {
                GameObject.Destroy(player.gameObject);
                _players.Remove(packet.playerId);

            }
        }
    }
}
{% endhighlight %}

* Assets\Scripts\PacketQueue.cs
{% highlight C# %}
public class PacketQueue
{
    ...
    public List<IPacket> PopAll()
    {
        List<IPacket> list = new List<IPacket>();

        lock (_lock)
        {
            while(_packetQueue.Count>0)
                list.Add(_packetQueue.Dequeue());
        }

        return list;
    }
}
{% endhighlight %}

### Test
  - you can change number of DummyClient Session count

<iframe width="560" height="315" src="/assets/video/posts/cshap_chatting_test/Chatting-Test.mp4" frameborder="0"> </iframe>

<figure>
  <a href="/assets/img/posts/cshap_chatting_test/18.jpg"><img src="/assets/img/posts/cshap_chatting_test/18.jpg"></a>
	<figcaption>C# Chatting Test</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
