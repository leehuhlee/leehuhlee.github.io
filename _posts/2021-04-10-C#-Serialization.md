---
layout: post
title: "[C#] Serialization"
date: 2021-04-10
excerpt: "Packet Serialization"
tags: [C#, Server]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part4/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part4: 게임 서버</a></center>

# Serialization
  - making packet any instance in memory, and put this packet in buffer
  - opposite side is deserialization

## Session Seperate
  - session is deligate
  - So, name is opposite from each

* Server\ClientSession.cs
{% highlight C# %}
class Packet
{
    public ushort size;
    public ushort packetId;
}

class PlayerInfoReq : Packet
{
    public long playerId;
}

class PlayerInfoOk : Packet
{
    public int hp;
    public int attack;
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ClientSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
        Thread.Sleep(5000);
        Disconnect();
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        switch ((PacketID)id)
        {
            case PacketID.PlayerInfoReq:
            {
                long playerId = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
                count += 8;
                Console.WriteLine($"PlaeyrInfoReq: {playerId}");
            }
            break;
        }

        Console.WriteLine($"RecvPacketId: {id}, RecvPacketSize: {size}");
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* Server\Program.cs
{% highlight C# %}
class Program
{
    static Listener _listener = new Listener();

    static void Main(string[] args)
    {
        string host = Dns.GetHostName();
        IPHostEntry ipHost = Dns.GetHostEntry(host);
        IPAddress ipAddr = ipHost.AddressList[0];
        IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

        _listener.Init(endPoint, () => { return new ClientSession(); });
        Console.WriteLine("Listening...");

        while (true) { }
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
class Packet
{
    public ushort size;
    public ushort packetId;
}

class PlayerInfoReq: Packet
{
    public long playerId;
}

class PlayerInfoOk: Packet
{
    public int hp;
    public int attack;
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        PlayerInfoReq packet = new PlayerInfoReq() { packetId = (ushort)PacketID.PlayerInfoReq, playerId = 1001 };

        {
            ArraySegment<byte> s = SendBufferHelper.Open(4096);
            
            // cursor
            ushort count = 0;
            bool success = true;

            // to avoid make byte array and use buffer directly
            // you can put array in span destination
            // it returns boolean
            count += 2;
            success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset + count, s.Count - count), packet.packetId);
            count += 2;
            success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset + count, s.Count - count), packet.playerId);
            count += 8;
            
            // you can put final size at the end moment
            success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset, s.Count), count);

            ArraySegment<byte> sendBuff = SendBufferHelper.Close(count);

            if(success)
                Send(sendBuff);
        }
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override int OnRecv(ArraySegment<byte> buffer)
    {
        string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
        Console.WriteLine($"[From Server] {recvData}");
        return buffer.Count;
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* DummyClient\Program.cs
{% highlight C# %}
class Program
{
    static void Main(string[] args)
    {
        string host = Dns.GetHostName();
        IPHostEntry ipHost = Dns.GetHostEntry(host);
        IPAddress ipAddr = ipHost.AddressList[0];
        IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

        Connector connector = new Connector();

        connector.Connect(endPoint, () => { return new ServerSession(); });

        while (true)
        {
            try{ }
            catch (Exception e)
            {
                Console.WriteLine(e.ToString());
            }

            Thread.Sleep(100);
        }
    }
}
{% endhighlight %}

### Test
<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/0.jpg"><img src="/assets/img/posts/cshap_serialization/0.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/1.jpg"><img src="/assets/img/posts/cshap_serialization/1.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

## Write and Read
  - Serialization and Deserialization

* Server\ClientSession.cs
{% highlight C# %}
public abstract class Packet
{
    public ushort size;
    public ushort packetId;

    public abstract ArraySegment<byte> Write();
    public abstract void Read(ArraySegment<byte> s);
}

class PlayerInfoReq : Packet
{
    public long playerId;

    public PlayerInfoReq()
    {
        this.packetId = (ushort)PacketID.PlayerInfoReq;
    }
    
    public override void Read(ArraySegment<byte> s)
    {
        ushort count = 0;

        count += 2;
        count += 2;

        this.playerId = BitConverter.ToInt64(new ReadOnlySpan<byte>(s.Array, s.Offset + count, s.Count - count));
        count += 8;
    }

    public override ArraySegment<byte> Write()
    {
        ArraySegment<byte> s = SendBufferHelper.Open(4096);

        ushort count = 0;
        bool success = true;

        count += 2;
        success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset + count, s.Count - count), this.packetId);
        count += 2;
        success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset + count, s.Count - count), this.playerId);
        count += 8;
        success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset, s.Count), count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ClientSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
        Thread.Sleep(5000);
        Disconnect();
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        switch ((PacketID)id)
        {
            // Deserialization
            case PacketID.PlayerInfoReq:
                {
                    PlayerInfoReq p = new PlayerInfoReq();
                    p.Read(buffer);
                    Console.WriteLine($"PlaeyrInfoReq: { p.playerId }");
                }
                break;

        }

        Console.WriteLine($"RecvPacketId: {id}, RecvPacketSize: {size}");
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
public abstract class Packet
{
    public ushort size;
    public ushort packetId;

    public abstract ArraySegment<byte> Write();
    public abstract void Read(ArraySegment<byte> s);
}

class PlayerInfoReq: Packet
{
    public long playerId;

    public PlayerInfoReq()
    {
        this.packetId = (ushort)PacketID.PlayerInfoReq;
    }
    
    public override void Read(ArraySegment<byte> s)
    {
        ushort count = 0;

        count += 2;
        count += 2;

        // to check there is enough space
        this.playerId = BitConverter.ToInt64(new ReadOnlySpan<byte>(s.Array, s.Offset + count, s.Count - count));
        count += 8;
    }

    public override ArraySegment<byte> Write()
    {
        ArraySegment<byte> s = SendBufferHelper.Open(4096);

        ushort count = 0;
        bool success = true;

        count += 2;
        success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset + count, s.Count - count), this.packetId);
        count += 2;
        success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset + count, s.Count - count), this.playerId);
        count += 8;
        success &= BitConverter.TryWriteBytes(new Span<byte>(s.Array, s.Offset, s.Count), count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        PlayerInfoReq packet = new PlayerInfoReq() { playerId = 1001 };

        {
            ArraySegment<byte> s = packet.Write();

            if(s != null)
                Send(s);
        }
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override int OnRecv(ArraySegment<byte> buffer)
    {
        string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
        Console.WriteLine($"[From Server] {recvData}");
        return buffer.Count;
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

### Test
<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/2.jpg"><img src="/assets/img/posts/cshap_serialization/2.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/3.jpg"><img src="/assets/img/posts/cshap_serialization/3.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

## resizable data
  - ushort is 2 bytes, and long is 8bytes
  - but you cannot decide string

* Serve\ClientSession.cs
{% highlight C# %}
public abstract class Packet
{
    public ushort size;
    public ushort packetId;

    public abstract ArraySegment<byte> Write();
    public abstract void Read(ArraySegment<byte> s);
}

class PlayerInfoReq : Packet
{
    public long playerId;
    public string name;

    public PlayerInfoReq()
    {
        this.packetId = (ushort)PacketID.PlayerInfoReq;
    }

    public override void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;

        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        count += sizeof(ushort);
        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);

        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
    }

    public override ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);

        ushort count = 0;
        bool success = true;

        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.packetId);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);

        ushort nameLen = (ushort)Encoding.Unicode.GetByteCount(this.name);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        Array.Copy(Encoding.Unicode.GetBytes(this.name), 0, segment.Array, count, nameLen);
        count += nameLen;

        success &= BitConverter.TryWriteBytes(s, count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ClientSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
        Thread.Sleep(5000);
        Disconnect();
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        switch ((PacketID)id)
        {
            case PacketID.PlayerInfoReq:
                {
                    PlayerInfoReq p = new PlayerInfoReq();
                    p.Read(buffer);
                    Console.WriteLine($"PlaeyrInfoReq: { p.playerId } {p.name}");
                }
                break;
        }

        Console.WriteLine($"RecvPacketId: {id}, RecvPacketSize: {size}");
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
public abstract class Packet
{
    public ushort size;
    public ushort packetId;

    public abstract ArraySegment<byte> Write();
    public abstract void Read(ArraySegment<byte> s);
}

class PlayerInfoReq: Packet
{
    public long playerId;
    public string name;

    public PlayerInfoReq()
    {
        this.packetId = (ushort)PacketID.PlayerInfoReq;
    }

    public override void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;

        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        count += sizeof(ushort);
        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);

        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        // Get string from bytes
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
    }

    public override ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);

        // count is packet size
        ushort count = 0;
        bool success = true;

        // to specify scope of segment and use Slice
        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.packetId);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);
            
        // get bytes from string
        // copy data first and then put data
        ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        count += nameLen;

        success &= BitConverter.TryWriteBytes(s, count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        PlayerInfoReq packet = new PlayerInfoReq() { playerId = 1001, name="ABCD" };

        {
            ArraySegment<byte> s = packet.Write();

            if(s != null)
                Send(s);
        }
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override int OnRecv(ArraySegment<byte> buffer)
    {
        string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
        Console.WriteLine($"[From Server] {recvData}");
        return buffer.Count;
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

### Test
<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/5.jpg"><img src="/assets/img/posts/cshap_serialization/5.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/6.jpg"><img src="/assets/img/posts/cshap_serialization/6.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

## List Packet

* Server\ClientSession.cs
{% highlight C# %}
public abstract class Packet
{
    public ushort size;
    public ushort packetId;

    public abstract ArraySegment<byte> Write();
    public abstract void Read(ArraySegment<byte> s);
}

class PlayerInfoReq : Packet
{
    public long playerId;
    public string name;

    public struct SkillInfo
    {
        public int id;
        public short level;
        public float duration;

        public bool Write(Span<byte> s, ref ushort count)
        {
            bool success = true;
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), id);
            count += sizeof(int);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), level);
            count += sizeof(short);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), duration);
            count += sizeof(float);
            return success;
        }

        public void Read(ReadOnlySpan<byte> s, ref ushort count)
        {
            id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
            count += sizeof(int);
            level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
            count += sizeof(short);
            duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
            count += sizeof(float);
        }
    }

    public List<SkillInfo> skills = new List<SkillInfo>();

    public PlayerInfoReq()
    {
        this.packetId = (ushort)PacketID.PlayerInfoReq;
    }

    public override void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;

        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        count += sizeof(ushort);
        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);

        // string
        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
        count += nameLen;

        // skill list
        skills.Clear();
        ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        for (int i = 0; i < skillLen; i++)
        {
            SkillInfo skill = new SkillInfo();
            skill.Read(s, ref count);
            skills.Add(skill);
        }
    }

    public override ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);

        ushort count = 0;
        bool success = true;

        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.packetId);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);

        // string
        ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        count += nameLen;

        // skill list
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)skills.Count);
        count += sizeof(ushort);
        foreach (SkillInfo skill in skills)
            success &= skill.Write(s, ref count);

        success &= BitConverter.TryWriteBytes(s, count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ClientSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
        Thread.Sleep(5000);
        Disconnect();
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        switch ((PacketID)id)
        {
            case PacketID.PlayerInfoReq:
                {
                    PlayerInfoReq p = new PlayerInfoReq();
                    p.Read(buffer);
                    Console.WriteLine($"PlaeyrInfoReq: { p.playerId } {p.name}");

                    foreach(PlayerInfoReq.SkillInfo skill in p.skills)
                    {
                        Console.WriteLine($"Skill({skill.id})({skill.level})({skill.duration})");
                    }
                }
                break;

        }

        Console.WriteLine($"RecvPacketId: {id}, RecvPacketSize: {size}");
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
public abstract class Packet
{
    public ushort size;
    public ushort packetId;

    public abstract ArraySegment<byte> Write();
    public abstract void Read(ArraySegment<byte> s);
}

class PlayerInfoReq: Packet
{
    public long playerId;
    public string name;
        
    public struct SkillInfo
    {
        public int id;
        public short level;
        public float duration;

        public bool Write(Span<byte> s, ref ushort count)
        {
            bool success = true;
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), id);
            count += sizeof(int);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), level);
            count += sizeof(short);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), duration);
            count += sizeof(float);
            return success;
        }

        public void Read(ReadOnlySpan<byte> s, ref ushort count)
        {
            id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
            count += sizeof(int);
            level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
            count += sizeof(short);
            // ToSingle is for float, ToDouble is for double
            duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
            count += sizeof(float);
        }
    }

    public List<SkillInfo> skills = new List<SkillInfo>();

    public PlayerInfoReq()
    {
        this.packetId = (ushort)PacketID.PlayerInfoReq;
    }

    public override void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;

        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        count += sizeof(ushort);
        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);

        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
        count += nameLen;

        // skill list
        // initialization
        skills.Clear();
        ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        for(int i=0; i<skillLen; i++)
        {
            SkillInfo skill = new SkillInfo();
            skill.Read(s, ref count);
            skills.Add(skill);
        }
    }

    public override ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);

        ushort count = 0;
        bool success = true;

        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.packetId);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);
            
        // string
        ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        count += nameLen;

        // skill list
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)skills.Count);
        count += sizeof(ushort);
        foreach(SkillInfo skill in skills)
            success &= skill.Write(s, ref count);

        success &= BitConverter.TryWriteBytes(s, count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        PlayerInfoReq packet = new PlayerInfoReq() { playerId = 1001, name="ABCD" };
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 101, level = 1, duration = 3.0f });
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 201, level = 2, duration = 4.0f });
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 301, level = 3, duration = 5.0f });
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 401, level = 4, duration = 6.0f });
        {
            ArraySegment<byte> s = packet.Write();

            if(s != null)
                Send(s);
        }
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override int OnRecv(ArraySegment<byte> buffer)
    {
        string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
        Console.WriteLine($"[From Server] {recvData}");
        return buffer.Count;
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

### Test
<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/4.jpg"><img src="/assets/img/posts/cshap_serialization/4.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/7.jpg"><img src="/assets/img/posts/cshap_serialization/7.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

# Packet Generator

## PacketFormat
  - Create PacketFormat Console app project

<figure>
  <a href="/assets/img/posts/cshap_serialization/9.jpg"><img src="/assets/img/posts/cshap_serialization/9.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

* DummyClient\ServerSession.cs
{% highlight C# %}
class PlayerInfoReq
{
    public long playerId;
    public string name;
    
    public struct SkillInfo
    {
        public int id;
        public short level;
        public float duration;

        public bool Write(Span<byte> s, ref ushort count)
        {
            bool success = true;
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), id);
            count += sizeof(int);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), level);
            count += sizeof(short);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), duration);
            count += sizeof(float);
            return success;
        }

        public void Read(ReadOnlySpan<byte> s, ref ushort count)
        {
            id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
            count += sizeof(int);
            level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
            count += sizeof(short);
            duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
            count += sizeof(float);
        }
    }

    public List<SkillInfo> skills = new List<SkillInfo>();

    public void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;

        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        count += sizeof(ushort);
        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);

        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
        count += nameLen;

        // skill list
        skills.Clear();
        ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        for(int i=0; i<skillLen; i++)
        {
            SkillInfo skill = new SkillInfo();
            skill.Read(s, ref count);
            skills.Add(skill);
        }
    }

    public ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);

        ushort count = 0;
        bool success = true;

        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);

        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)PacketID.PlayerInfoReq);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);
        
        // string
        ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        count += nameLen;

        // skill list
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)skills.Count);
        count += sizeof(ushort);
        foreach(SkillInfo skill in skills)
            success &= skill.Write(s, ref count);

        success &= BitConverter.TryWriteBytes(s, count);

        if (success == false)
            return null;

        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        PlayerInfoReq packet = new PlayerInfoReq() { playerId = 1001, name="ABCD" };
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 101, level = 1, duration = 3.0f });
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 201, level = 2, duration = 4.0f });
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 301, level = 3, duration = 5.0f });
        packet.skills.Add(new PlayerInfoReq.SkillInfo() { id = 401, level = 4, duration = 6.0f });
        {
            ArraySegment<byte> s = packet.Write();

            if(s != null)
                Send(s);
        }
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override int OnRecv(ArraySegment<byte> buffer)
    {
        string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
        Console.WriteLine($"[From Server] {recvData}");
        return buffer.Count;
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* PacketFormat\PDL.xml
  - for Session automatic
  - packet definition

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<PDL>
  <packet name="PlayerInfoReq">
    <long name="playerId"/>
    <string name="name"/>
    <list name="skill">
      <int name="id"/>
      <short name="level"/>
      <float name="duration"/>
    </list>
  </packet>
</PDL>
{% endhighlight %}

  - copy `PDL.xml` to `\PacketGenerator\bin\Debug\netcoreapp3.1`
<figure>
  <a href="/assets/img/posts/cshap_serialization/10.jpg"><img src="/assets/img/posts/cshap_serialization/10.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

* PacketForamt\Program.cs
{% highlight C# %}
class Program
{
    static void Main(string[] args)
    {
        // parsing xml
        XmlReaderSettings settings = new XmlReaderSettings()
        {
            IgnoreComments = true,
            IgnoreWhitespace = true
        };
        
        // skip dispose in code
        using(XmlReader r = XmlReader.Create("PDL.xml", settings))
        {
            r.MoveToContent();

            while (r.Read())
            {
                // r.Depth == 1 : this is packet
                // r.NodeType == XmlNodeType.Element : this is start tag
                if (r.Depth == 1 && r.NodeType == XmlNodeType.Element)
                    ParsePacket(r);
            }
        }
    }

    public static void ParsePacket(XmlReader r)
    {
        // r.NodeType == XmlNodeType.EndElement : this is end tag
        if (r.NodeType == XmlNodeType.EndElement)
            return;

        if (r.Name.ToLower() != "packet")
        {
            Console.WriteLine("Invalied packet node");
            return;
        }

        string packetName = r["name"];
        if (string.IsNullOrEmpty(packetName))
        {
            Console.WriteLine("Packet without name");
            return;
        }

        ParseMembers(r);
    }

    public static void ParseMembers(XmlReader r)
    {
        string packetName = r["name"];

        // depth of inside packet
        int depth = r.Depth + 1;
        while (r.Read())
        {
            if (r.Depth != depth)
                break;

            // member name check
            string memberName = r["name"];
            if (string.IsNullOrEmpty(memberName))
            {
                Console.WriteLine("Member without name");
                return;
            }

            // member type check
            string memberType = r.Name.ToLower();
            switch (memberType)
            {
                case "bool":
                case "byte":
                case "short":
                case "ushort":
                case "int":
                case "long":
                case "float":
                case "double":
                case "string":
                case "list":
                    break;
                default:
                    break;

            }
        }
    }
}
{% endhighlight %}

* PacketGenerator\PacketFormat.cs
  - you can define several lines of string by `@`
  - you should `{{` and `}}` here

{% highlight C# %}
class PacketFormat
{
    // {0} Packet name
    // {1} Member variable
    // {2} Member variable Read
    // {3} Member variable Write
    public static string packetFormat =
@"class {0}
{
    {1}   

    public void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;
        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        count += sizeof(ushort);

        {2}
    }

    public ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);
        ushort count = 0;
        bool success = true;
        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)PacketID.{0});
        count += sizeof(ushort);

        {3}

        success &= BitConverter.TryWriteBytes(s, count);
        if (success == false)
            return null;
        return SendBufferHelper.Close(count);
    }
}";

    // {0} variable type
    // {1} variable name
    public static string memberFormat = 
@"public {0} {1}";

    // {0} variable name
    // {1} To~ variable type
    // {2} vatiable type
    public static string readFormat =
@"this.{0} = BitConverter.{1}(s.Slice(count, s.Length - count));
count += sizeof({2});";

    // {0} variable name
    public static string readStringFormat =
@"ushort {0}Len = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
count += sizeof(ushort);
this.{0} = Encoding.Unicode.GetString(s.Slice(count, {0}Len));
count += {0}Len;";

    // {0} variable name
    // {1} variable type
    public static string writeFormat =
@"success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.{0});
count += sizeof({1});";

    // {0} variable name
    public static string writeStringFormat =
@"ushort {0}Len = (ushort)Encoding.Unicode.GetBytes(this.{0}, 0, this.{0}.Length, segment.Array, segment.Offset + count + sizeof(ushort));
success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), {0}Len);
count += sizeof(ushort);
count += {0}Len;";
}
{% endhighlight %}

## Packet Generator

* PacketGenerator\PacketFormat.cs
{% highlight C# %}
class PacketFormat
{
    // {0} Packet Name
    // {1} Member Variable
    // {2} Member Variable Read
    // {3} Member Variable Write
    public static string packetFormat =
@"class {0}
{
    {1}   
    
    public void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;
        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        count += sizeof(ushort);

        {2}
    }

    public ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);
        ushort count = 0;
        bool success = true;
        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)PacketID.{0});
        count += sizeof(ushort);

        {3}

        success &= BitConverter.TryWriteBytes(s, count);
        if (success == false)
            return null;
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
@"public struct {0}
{   
    {2}

    public void Read(ReadOnlySpan<byte> s, ref ushort count)
    {
        {3}
    }

    public bool Write(Span<byte> s, ref ushort count)
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
@"this.{0} = BitConverter.{1}(s.Slice(count, s.Length - count));
count += sizeof({2});";

    // {0} Variable Name
    public static string readStringFormat =
@"ushort {0}Len = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
count += sizeof(ushort);
this.{0} = Encoding.Unicode.GetString(s.Slice(count, {0}Len));
count += {0}Len;";

    // {0} List Name[Capital]
    // {1} List Name[small]
    public static string readListFormat =
@"this.{1}s.Clear();
ushort {1}Len = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
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
 @"success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.{0});
count += sizeof({1});";

    // {0} Variable Name
    public static string writeStringFormat =
@"ushort {0}Len = (ushort)Encoding.Unicode.GetBytes(this.{0}, 0, this.{0}.Length, segment.Array, segment.Offset + count + sizeof(ushort));
success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), {0}Len);
count += sizeof(ushort);
count += {0}Len;";

    // {0} List Name[Capital]
    // {1} List Name[small]
    public static string writeListFormat =
@"success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)this.{1}s.Count);
count += sizeof(ushort);
foreach({0} {1} in {1}s)
    success &= {1}.Write(s, ref count);";
}
{% endhighlight %}

* PacketGenerator\Program.cs
{% highlight C# %}
class Program
{
    static string genPackets;

    static void Main(string[] args)
    {
        XmlReaderSettings settings = new XmlReaderSettings()
        {
            IgnoreComments = true,
            IgnoreWhitespace = true
        };
        
        using(XmlReader r = XmlReader.Create("PDL.xml", settings))
        {
            r.MoveToContent();

            while (r.Read())
            {
                if (r.Depth == 1 && r.NodeType == XmlNodeType.Element)
                    ParsePacket(r);
            }

            // Create File
            File.WriteAllText("GenPackets.cs", genPackets);
        }
    }

    public static void ParsePacket(XmlReader r)
    {
        if (r.NodeType == XmlNodeType.EndElement)
            return;

        if (r.Name.ToLower() != "packet")
        {
            Console.WriteLine("Invalied packet node");
            return;
        }

        string packetName = r["name"];
        if (string.IsNullOrEmpty(packetName))
        {
            Console.WriteLine("Packet without name");
            return;
        }

        // save Member Variables, Member Variable Read, Member Variable Write
        Tuple<string, string, string> t = ParseMembers(r);
        // save packet
        // Packet Format,  Packet Name, Member Variables, Member Variable Read, Member Variable Write
        genPackets += string.Format(PacketFormat.packetFormat, packetName, t.Item1, t.Item2, t.Item3);
    }

    public static Tuple<string, string, string> ParseMembers(XmlReader r)
    {
        string packetName = r["name"];

        string memberCode = "";
        string readCode = "";
        string writeCode = "";

        int depth = r.Depth + 1;
        while (r.Read())
        {
            if (r.Depth != depth)
                break;

            string memberName = r["name"];
            if (string.IsNullOrEmpty(memberName))
            {
                Console.WriteLine("Member without name");
                return null;
            }

            if (string.IsNullOrEmpty(memberCode) == false)
                memberCode += Environment.NewLine;
            if (string.IsNullOrEmpty(readCode) == false)
                readCode += Environment.NewLine;
            if (string.IsNullOrEmpty(writeCode) == false)
                writeCode += Environment.NewLine;

            string memberType = r.Name.ToLower();
            switch (memberType)
            {
                case "bool":
                case "byte":
                case "short":
                case "ushort":
                case "int":
                case "long":
                case "float":
                case "double":
                    memberCode += string.Format(PacketFormat.memberFormat, memberType, memberName);
                    readCode += string.Format(PacketFormat.readFormat, memberName, ToMemberType(memberType), memberType);
                    writeCode += string.Format(PacketFormat.writeFormat, memberName, memberType);
                    break;
                case "string":
                    memberCode += string.Format(PacketFormat.memberFormat, memberType, memberName);
                    readCode += string.Format(PacketFormat.readStringFormat, memberName);
                    writeCode += string.Format(PacketFormat.writeStringFormat, memberName);
                    break;
                case "list":
                    Tuple<string, string, string> t = ParseList(r);
                    memberCode += t.Item1;
                    readCode += t.Item2;
                    writeCode += t.Item3;
                    break;
                default:
                    break;

            }
        }

        memberCode = memberCode.Replace("\n", "\n\t");
        readCode = readCode.Replace("\n", "\n\t\t");
        writeCode = writeCode.Replace("\n", "\n\t\t");
        return new Tuple<string, string, string>(memberCode, readCode, writeCode);
    }

    public static Tuple<string, string, string> ParseList(XmlReader r)
    {
        string listName = r["name"];
        if (string.IsNullOrEmpty(listName))
        {
            Console.WriteLine("List without name");
            return null;
        }

        Tuple<string, string, string> t = ParseMembers(r);

        string memberCode = string.Format(PacketFormat.memberListFormat,
            FirstCharToUpper(listName), 
            FirstCharToLower(listName), 
            t.Item1, 
            t.Item2, 
            t.Item3);

        string readCode = string.Format(PacketFormat.readListFormat,
            FirstCharToUpper(listName),
            FirstCharToLower(listName));

        string writeCode = string.Format(PacketFormat.writeListFormat,
            FirstCharToUpper(listName),
            FirstCharToLower(listName));

        return new Tuple<string, string, string>(memberCode, readCode, writeCode);
    }

    public static string ToMemberType(string memberType)
    {
        switch (memberType)
        {
            case "bool":
                return "ToBoolean";
            case "short":
                return "ToInt16";
            case "ushort":
                return "ToUInt16";
            case "int":
                return "ToInt32";
            case "long":
                return "ToInt64";
            case "float":
                return "ToSingle";
            case "double":
                return "ToDouble";
            default:
                return "";
        }
    }

    public static string FirstCharToUpper(string input)
    {
        if (string.IsNullOrEmpty(input))
            return "";
        return input[0].ToString().ToUpper() + input.Substring(1);
    }

    public static string FirstCharToLower(string input)
    {
        if (string.IsNullOrEmpty(input))
            return "";
        return input[0].ToString().ToLower() + input.Substring(1);
    }
}
{% endhighlight %}

* Excute Packet Generator project
  - as result, it makes `GenPacket.cs`
  - copy whole code in `GenPacket.cs` and paste it on `ClientSession.cs` and `ServerSession.cs` 

<figure>
  <a href="/assets/img/posts/cshap_serialization/13.jpg"><img src="/assets/img/posts/cshap_serialization/13.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

* Server\ClientSession.cs
{% highlight C# %}
class PlayerInfoReq
{
    public long playerId;
    public string name;

    public struct Skill
    {
        public int id;
        public short level;
        public float duration;

        public void Read(ReadOnlySpan<byte> s, ref ushort count)
        {
            this.id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
            count += sizeof(int);
            this.level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
            count += sizeof(short);
            this.duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
            count += sizeof(float);
        }

        public bool Write(Span<byte> s, ref ushort count)
        {
            bool success = true;
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.id);
            count += sizeof(int);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.level);
            count += sizeof(short);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.duration);
            count += sizeof(float);
            return success;
        }
    }

    public List<Skill> skills = new List<Skill>();

    public void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;
        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        count += sizeof(ushort);

        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);
        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
        count += nameLen;
        this.skills.Clear();
        ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        for (int i = 0; i < skillLen; i++)
        {
            Skill skill = new Skill();
            skill.Read(s, ref count);
            skills.Add(skill);
        }
    }

    public ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);
        ushort count = 0;
        bool success = true;
        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)PacketID.PlayerInfoReq);
        count += sizeof(ushort);

        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);
        ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        count += nameLen;
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)this.skills.Count);
        count += sizeof(ushort);
        foreach (Skill skill in skills)
            success &= skill.Write(s, ref count);

        success &= BitConverter.TryWriteBytes(s, count);
        if (success == false)
            return null;
        return SendBufferHelper.Close(count);
    }
}

public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ClientSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
        Thread.Sleep(5000);
        Disconnect();
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        switch ((PacketID)id)
        {
            case PacketID.PlayerInfoReq:
                {
                    PlayerInfoReq p = new PlayerInfoReq();
                    p.Read(buffer);
                    Console.WriteLine($"PlaeyrInfoReq: { p.playerId } {p.name}");

                    foreach(PlayerInfoReq.Skill skill in p.skills)
                    {
                        Console.WriteLine($"Skill({skill.id})({skill.level})({skill.duration})");
                    }
                }
                break;
        }

        Console.WriteLine($"RecvPacketId: {id}, RecvPacketSize: {size}");
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
class PlayerInfoReq
{
    public long playerId;
    public string name;

    public struct Skill
    {
        public int id;
        public short level;
        public float duration;

        public void Read(ReadOnlySpan<byte> s, ref ushort count)
        {
            this.id = BitConverter.ToInt32(s.Slice(count, s.Length - count));
            count += sizeof(int);
            this.level = BitConverter.ToInt16(s.Slice(count, s.Length - count));
            count += sizeof(short);
            this.duration = BitConverter.ToSingle(s.Slice(count, s.Length - count));
            count += sizeof(float);
        }

        public bool Write(Span<byte> s, ref ushort count)
        {
            bool success = true;
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.id);
            count += sizeof(int);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.level);
            count += sizeof(short);
            success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.duration);
            count += sizeof(float);
            return success;
        }
    }

    public List<Skill> skills = new List<Skill>();

    public void Read(ArraySegment<byte> segment)
    {
        ushort count = 0;
        ReadOnlySpan<byte> s = new ReadOnlySpan<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        count += sizeof(ushort);

        this.playerId = BitConverter.ToInt64(s.Slice(count, s.Length - count));
        count += sizeof(long);
        ushort nameLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        this.name = Encoding.Unicode.GetString(s.Slice(count, nameLen));
        count += nameLen;
        this.skills.Clear();
        ushort skillLen = BitConverter.ToUInt16(s.Slice(count, s.Length - count));
        count += sizeof(ushort);
        for (int i = 0; i < skillLen; i++)
        {
            Skill skill = new Skill();
            skill.Read(s, ref count);
            skills.Add(skill);
        }
    }

    public ArraySegment<byte> Write()
    {
        ArraySegment<byte> segment = SendBufferHelper.Open(4096);
        ushort count = 0;
        bool success = true;
        Span<byte> s = new Span<byte>(segment.Array, segment.Offset, segment.Count);
        count += sizeof(ushort);
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)PacketID.PlayerInfoReq);
        count += sizeof(ushort);

        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), this.playerId);
        count += sizeof(long);
        ushort nameLen = (ushort)Encoding.Unicode.GetBytes(this.name, 0, this.name.Length, segment.Array, segment.Offset + count + sizeof(ushort));
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), nameLen);
        count += sizeof(ushort);
        count += nameLen;
        success &= BitConverter.TryWriteBytes(s.Slice(count, s.Length - count), (ushort)this.skills.Count);
        count += sizeof(ushort);
        foreach (Skill skill in skills)
            success &= skill.Write(s, ref count);

        success &= BitConverter.TryWriteBytes(s, count);
        if (success == false)
            return null;
        return SendBufferHelper.Close(count);
    }
}


public enum PacketID
{
    PlayerInfoReq = 1,
    PlayerInfoOk = 2,
}

class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");

        PlayerInfoReq packet = new PlayerInfoReq() { playerId = 1001, name="ABCD" };
        packet.skills.Add(new PlayerInfoReq.Skill() { id = 101, level = 1, duration = 3.0f });
        packet.skills.Add(new PlayerInfoReq.Skill() { id = 201, level = 2, duration = 4.0f });
        packet.skills.Add(new PlayerInfoReq.Skill() { id = 301, level = 3, duration = 5.0f });
        packet.skills.Add(new PlayerInfoReq.Skill() { id = 401, level = 4, duration = 6.0f });
        {
            ArraySegment<byte> s = packet.Write();

            if(s != null)
                Send(s);
        }
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override int OnRecv(ArraySegment<byte> buffer)
    {
        string recvData = Encoding.UTF8.GetString(buffer.Array, buffer.Offset, buffer.Count);
        Console.WriteLine($"[From Server] {recvData}");
        return buffer.Count;
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

### Test
<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/11.jpg"><img src="/assets/img/posts/cshap_serialization/11.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/12.jpg"><img src="/assets/img/posts/cshap_serialization/12.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

## Byte Packet

* PacketGenerator\bin\Debug\netcoreapp3.1\PDL.xml
{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<PDL>
  <packet name="PlayerInfoReq">
    <byte name="testByte"/>
    <long name="playerId"/>
    <string name="name"/>
    <list name="skill">
      <int name="id"/>
      <short name="level"/>
      <float name="duration"/>
      <list name="attribute">
        <int name="att"/>
      </list>
    </list>
  </packet>
  <packet name="Test">
    <int name="textInt"/>
  </packet>
</PDL>
{% endhighlight %}

* PacketGenerator\PacketFormat.cs
{% highlight C# %}
// {0} Packet Name/Number List
// {1} Packet List
class PacketFormat
{
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

{1}";

    // {0} Packet Name
    // {1} Packet Number
    public static string packetEnumFormat = 
@"{0} = {1},";

...

    public static string memberListFormat =
@"public class {0}
{
    {2}

    public void Read(ReadOnlySpan<byte> s, ref ushort count)
    {   
        {3}
    }

    public bool Write(Span<byte> s, ref ushort count)
    {
        bool success = true;
        {4}
        return success;
    }
}

public List<{0}> {1}s = new List<{0}>();";

...

    // {0} Variable Name
    // {1} Variable Type
    public static string readByteFormat =
@"this.{0} = ({1})segment.Array[segment.Offset + count];
count += sizeof({1});";

...

    // {0} Variable Name
    // {1} Variable Type
    public static string writeByteFormat =
@"segment.Array[segment.Offset + count] = ({1})this.{0} ;
count += sizeof({1});";

...
{% endhighlight %}

* PacketGenerator\Program.cs
{% highlight C# %}
class Program
{
    static string genPackets;
    static ushort packetId;
    static string packetEnums;

    static void Main(string[] args)
    {
        ...
        
        using(XmlReader r = XmlReader.Create("PDL.xml", settings))
        {
           ...

            string fileText = string.Format(PacketFormat.fileFormat, packetEnums, genPackets);
            File.WriteAllText("GenPackets.cs", fileText);
        }
    }

    public static void ParsePacket(XmlReader r)
    {
        ...
        packetEnums += string.Format(PacketFormat.packetEnumFormat, packetName, ++packetId) + Environment.NewLine + "\t";
    }

    public static Tuple<string, string, string> ParseMembers(XmlReader r)
    {
        ...
        while (r.Read())
        {
            ...
            switch (memberType)
            {
                case "byte":
                case "sbyte":
                    memberCode += string.Format(PacketFormat.memberFormat, memberType, memberName);
                    readCode += string.Format(PacketFormat.readByteFormat, memberName, memberType);
                    writeCode += string.Format(PacketFormat.writeByteFormat, memberName, memberType);
                    break;
                ...

            }
        }
    }
{% endhighlight %}

* Server\ClientSession.cs
  - copy `GenPacket.cs` and paste it

{% highlight C# %}
...
class ClientSession : PacketSession
{
   ...

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
       ...

        switch ((PacketID)id)
        {
            case PacketID.PlayerInfoReq:
                {
                    ...
                    foreach(PlayerInfoReq.Skill skill in p.skills)
                    {
                        Console.WriteLine($"Skill({skill.id})({skill.level})({skill.duration})({skill.attributes.Count})");
                    }
                }
                break;

        }
        ...
    }
    ...
}
{% endhighlight %}

* DummyClient\ServerSession.cs
{% highlight C# %}
class ServerSession : Session
{
    public override void OnConnected(EndPoint endPoint)
    {
        ...
        var skill = new PlayerInfoReq.Skill() { id = 101, level = 1, duration = 3.0f };
        skill.attributes.Add(new PlayerInfoReq.Skill.Attribute() { att = 7 });
        packet.skills.Add(skill);
        ...
    }
    ...
}
{% endhighlight %}

### Test
<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/14.jpg"><img src="/assets/img/posts/cshap_serialization/14.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/15.jpg"><img src="/assets/img/posts/cshap_serialization/15.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

# batch file
  - Create `DummyClient\Packet\GenPackets.cs` and `Server\Packet\GenPackets.cs`

<figure>
  <a href="/assets/img/posts/cshap_serialization/16.jpg"><img src="/assets/img/posts/cshap_serialization/16.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

* PacketGenerator\Program.cs
{% highlight C# %}
class Program
{
    ...

    static void Main(string[] args)
    {
        // define path for PDL
        string pdlPath = "../PDL.xml";

        XmlReaderSettings settings = new XmlReaderSettings()
        {
            IgnoreComments = true,
            IgnoreWhitespace = true
        };

        // you can change path by argument
        if (args.Length >= 1)
            pdlPath = args[0];
            
        using(XmlReader r = XmlReader.Create(pdlPath, settings))
        {
           ...
        }
    }
    ...
}
{% endhighlight %}

## bin folder
  - you can make your debug files on bin folder by setting

<figure>
  <a href="/assets/img/posts/cshap_serialization/17.jpg"><img src="/assets/img/posts/cshap_serialization/17.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

* PacketGenerator\PacketGenerator.csproj
{% highlight csproj %}
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp3.1</TargetFramework>
    // set flag false for appending target framework to output path
    <AppendTargetFrameworkToOutputPath>false</AppendTargetFrameworkToOutputPath>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
    <OutputPath>bin\</OutputPath>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
    <OutputPath>bin\</OutputPath>
  </PropertyGroup>

</Project>
{% endhighlight %}

## batch file
* Common\Packet\GenPackets.bat
{% highlight bat %}
START ../../PacketGenerator/bin/PacketGenerator.exe ../../PacketGenerator/PDL.xml
XCOPY /Y GenPackets.cs "../../DummyClient/Packet"
XCOPY /Y GenPackets.cs "../../Server/Packet"
{% endhighlight %}
  - XCOPY : copy file
  - /Y : rewrite, if there is already the file

# Packet Handler and Manager
  - make Packet case automatically

* PacketGenerator\PacketFormat.cs
{% highlight C# %}
class PacketFormat
{
    public static string fileFormat =
@"using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using ServerCore;

...

// base packet for all packets
interface IPacket
{
    ushort Protocol { get; }
    void Read(ArraySegment<byte> segment);
    ArraySegment<byte> Write();
}


{1}";

public static string packetFormat =
@"class {0} : IPacket
{
    ...

    public ushort Protocol 
    { 
        get 
        { 
            return (ushort)PacketID.{0}; 
        }
    }
    
    ...
}";
{% endhighlight %}

* Server\Packet\PacketHandler.cs
  - set namespace to `Server`

{% highlight C# %}
class PacketHandler
{
    public static void PlayerInfoReqHandler(PacketSession session, IPacket packet)
    {
        PlayerInfoReq p = packet as PlayerInfoReq;

        Console.WriteLine($"PlaeyrInfoReq: { p.playerId } {p.name}");

        foreach (PlayerInfoReq.Skill skill in p.skills)
        {
            Console.WriteLine($"Skill({skill.id})({skill.level})({skill.duration})({skill.attributes.Count})");
        }
    }
}
{% endhighlight %}

* Server\Packet\PacketManager.cs
  - set namespace to `Server`

{% highlight C# %}
class PacketManager
{
    // PacketManager exists only one
    #region Singleton
    static PacketManager _instance;
    
    public static PacketManager Instance
    {
        get
        {
            if (_instance == null)
                _instance = new PacketManager();
            return _instance;
        }
    }
    #endregion

    Dictionary<ushort, Action<PacketSession, ArraySegment<byte>>> _onRecv = new Dictionary<ushort, Action<PacketSession, ArraySegment<byte>>>();
    Dictionary<ushort, Action<PacketSession, IPacket>> _handler = new Dictionary<ushort, Action<PacketSession, IPacket>>();

    // for automatic packet
    public void Register()
    {
        _onRecv.Add((ushort)PacketID.PlayerInfoReq, MakePacket<PlayerInfoReq>);
        _handler.Add((ushort)PacketID.PlayerInfoReq, PacketHandler.PlayerInfoReqHandler);
    }

    public void OnRecvPacket(PacketSession session, ArraySegment<byte> buffer)
    {
        ushort count = 0;
        ushort size = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
        count += 2;
        ushort id = BitConverter.ToUInt16(buffer.Array, buffer.Offset + count);
        count += 2;

        // change switch case to action
        // search by _onRecv because _onRecv has MakePacket
        Action<PacketSession, ArraySegment<byte>> action = null;
        if (_onRecv.TryGetValue(id, out action))
            action.Invoke(session, buffer);
    }

    // Condition: T should make interface IPacket, and can use new method
    void MakePacket<T>(PacketSession session, ArraySegment<byte> buffer) where T: IPacket, new()
    {
        // make packet
        T pkt = new T();
        pkt.Read(buffer);

        // call handler
        Action<PacketSession, IPacket> action = null;
        if (_handler.TryGetValue(pkt.Protocol, out action))
            action.Invoke(session, pkt);
    }
}
{% endhighlight %}

* Server\ClientSession.cs
{% highlight C# %}
class ClientSession : PacketSession
{
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected: {endPoint}");
        Thread.Sleep(5000);
        Disconnect();
    }

    public override void OnRecvPacket(ArraySegment<byte> buffer)
    {
        PacketManager.Instance.OnRecvPacket(this, buffer);
    }

    public override void OnDisconnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnDisconnected: {endPoint}");
    }

    public override void OnSend(int numOfBytes)
    {
        Console.WriteLine($"Transferred bytes: {numOfBytes}");
    }
}
{% endhighlight %}

* Server\Program.cs
{% highlight C# %}
class Program
{
    static Listener _listener = new Listener();

    static void Main(string[] args)
    {
        // Initialize without multi thread
        PacketManager.Instance.Register();
        ...
    }
}
{% endhighlight %}

* DummyClient\ServerSession.cs
  - Delete part of `GenPacket.cs`

<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/18.jpg"><img src="/assets/img/posts/cshap_serialization/18.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/19.jpg"><img src="/assets/img/posts/cshap_serialization/19.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

## Packet Manager

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
    static PacketManager _instance;
    
    public static PacketManager Instance
    {
        get
        {
            if (_instance == null)
                _instance = new PacketManager();
            return _instance;
        }
    }
    #endregion

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
        ...
}
{% endhighlight %}

* PacketGenerator\PDL.xml
  - `C_` is for client
  - `S_` is for server
{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<PDL>
  <packet name="C_PlayerInfoReq">
    <byte name="testByte"/>
    <long name="playerId"/>
    <string name="name"/>
    <list name="skill">
      <int name="id"/>
      <short name="level"/>
      <float name="duration"/>
      <list name="attribute">
        <int name="att"/>
      </list>
    </list>
  </packet>
  <packet name="S_Test">
    <int name="textInt"/>
  </packet>
</PDL>
{% endhighlight %}

* PacketGenerator\Program.cs
{% highlight C# %}
class Program
{
    ...

    static string clientRegister;
    static string serverRegister;

    static void Main(string[] args)
    {
        ...

        using(XmlReader r = XmlReader.Create(pdlPath, settings))
        {
            ...
            string fileText = string.Format(PacketFormat.fileFormat, packetEnums, genPackets);
            File.WriteAllText("GenPackets.cs", fileText);
            string ClientManagerText = string.Format(PacketFormat.managerFormat, clientRegister);
            File.WriteAllText("ClientPacketManager.cs", ClientManagerText);
            string ServerManagerText = string.Format(PacketFormat.managerFormat, serverRegister);
            File.WriteAllText("ServerPacketManager.cs", ServerManagerText);
        }
    }

    public static void ParsePacket(XmlReader r)
    {
        ...

        if(packetName.StartsWith("S_") || packetName.StartsWith("s_"))
            clientRegister += string.Format(PacketFormat.managerRegisterFormat, packetName) + Environment.NewLine;
        else
            serverRegister += string.Format(PacketFormat.managerRegisterFormat, packetName) + Environment.NewLine;
    }
}
{% endhighlight %}

* Common\Packet\GenPackets.bat
{% highlight bat}
START ../../PacketGenerator/bin/PacketGenerator.exe ../../PacketGenerator/PDL.xml
XCOPY /Y GenPackets.cs "../../DummyClient/Packet"
XCOPY /Y GenPackets.cs "../../Server/Packet"
XCOPY /Y ClientPacketManager.cs "../../DummyClient/Packet"
XCOPY /Y ServerPacketManager.cs "../../Server/Packet"
{% endhighlight %}



<figure class="half">
  <a href="/assets/img/posts/cshap_serialization/20.jpg"><img src="/assets/img/posts/cshap_serialization/20.jpg"></a>
  <a href="/assets/img/posts/cshap_serialization/21.jpg"><img src="/assets/img/posts/cshap_serialization/21.jpg"></a>
	<figcaption>C# Serialization</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
