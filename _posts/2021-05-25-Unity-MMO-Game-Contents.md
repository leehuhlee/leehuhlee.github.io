---
layout: post
title: "[Unity] MMO Game Contents"
date: 2021-05-25
excerpt: "MMO Game Contents"
tags: [C#, Unity, Game, MMO, 2D, DB]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part9/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part9: MMO 컨텐츠 구현 (DB연동 + 대형 구조 + 라이브)</a></center>

# DB

## DB Link

* Nuget Package Manager
  - Microsoft.EntityFrameworkCore.SqlServer
  - Microsoft.EntityFrameworkCore.Tools
  - Microsoft.Extentions.Logging.Console

<figure class="third">
  <a href="/assets/img/posts/unity_mmocontents/0.jpg"><img src="/assets/img/posts/unity_mmocontents/0.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/1.jpg"><img src="/assets/img/posts/unity_mmocontents/1.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/2.jpg"><img src="/assets/img/posts/unity_mmocontents/2.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Server\DB\AppDbContext.cs
{% highlight C# %}
public class AppDbContext: DbContext
{
    public DbSet<AccountDb> Accounts { get; set; }
    public DbSet<PlayerDb> Players { get; set; }

    static readonly ILoggerFactory _logger = LoggerFactory.Create( builder => { builder.AddConsole(); });

    // Input your SQL Server
    string _connectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=GameDB;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";

    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
        options
            // Write on Console
            .UseLoggerFactory(_logger) 
            // Use config.json instead of hard coding
            .UseSqlServer(ConfigManager.Config == null ? _connectionString : ConfigManager.Config.connectionString);
    }

    protected override void  OnModelCreating(ModelBuilder builder)
    {
        builder.Entity<AccountDb>()
            .HasIndex(a => a.AccountName)
            .IsUnique();

        builder.Entity<PlayerDb>()
            .HasIndex(p => p.PlayerName)
            .IsUnique();
    }
}
{% endhighlight %}

* Server\bin\Debug\netcoreapp3.1\config.json
{% highlight C# %}
{
  "dataPath": "../../../../../Client/Assets/Resources/Data",
  "connectionString": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=GameDB;"
}
{% endhighlight %}

* Server\DB\DataModel.cs
{% highlight C# %}
[Table("Account")]
public class AccountDb
{
    public int AccountDbId { get; set; }
    public string AccountName { get; set; }
    public ICollection<PlayerDb> Players { get; set; }
}

[Table("Player")]
public class PlayerDb
{
    public int PlayerDbId { get; set; }
    public string PlayerName { get; set; }
    public AccountDb Account { get; set; }
}
{% endhighlight %}

* Server\Data\ConfigManager.cs
{% highlight C# %}
[Serializable]
public class ServerConfig
{
    public string dataPath;
    public string connectionString;
}
...
{% endhighlight %}

* Package Manager Console
  - add-migration Init
  - update-database

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/3.jpg"><img src="/assets/img/posts/unity_mmocontents/3.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/4.jpg"><img src="/assets/img/posts/unity_mmocontents/4.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

### Test

* Server\Program.cs
{% highlight C# %}
class Program
{
    ...
    static void Main(string[] args)
    {
        ConfigManager.LoadConfig();
        DataManager.LoadData();

        // DB Test
        using(AppDbContext db = new AppDbContext())
        {
            db.Accounts.Add(new AccountDb() { AccountName = "TestAccount" });
            db.SaveChanges();
        }
        ...
    }
    ...
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/unity_mmocontents/5.jpg"><img src="/assets/img/posts/unity_mmocontents/5.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

## Access

* Common\protoc-3.12.3-win64\bin\Protocol.proto
{% highlight proto %}
...
enum MsgId {
  S_ENTER_GAME = 0;
  S_LEAVE_GAME = 1;
  S_SPAWN = 2;
  S_DESPAWN = 3;
  C_MOVE = 4;
  S_MOVE = 5;
  C_SKILL = 6;
  S_SKILL = 7;
  S_CHANGE_HP = 8;
  S_DIE = 9;
  S_CONNECTED = 10;
  C_LOGIN = 11;
  S_LOGIN = 12;
}
...

message S_Connected{
}

message C_Login{
  string uniqueId = 1;
}

message S_Login{
  int32 loginOk = 1;
}
...
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
public class ClientSession : PacketSession
{
    ...
    public override void OnConnected(EndPoint endPoint)
    {
        Console.WriteLine($"OnConnected : {endPoint}");

        // Connection Resqawn
        {
            S_Connected connectedPacket = new S_Connected();
            Send(connectedPacket);
        }
        ...
    }
}
{% endhighlight %}

* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    ...
    public static void C_LoginHandler(PacketSession session, IMessage packet)
	{
		C_Login loginPacket = packet as C_Login;
		ClientSession clientSession = session as ClientSession;

        Console.WriteLine($"UniqueId({loginPacket.UniqueId})");

		// TODO: Security Check

		//
		using(AppDbContext db = new AppDbContext())
        {
			AccountDb findAccount = db.Accounts.Where(a => a.AccountName == loginPacket.UniqueId).FirstOrDefault();

            // exist account
			if(findAccount != null)
            {
				S_Login loginOk = new S_Login() { LoginOk = 1 };
				clientSession.Send(loginOk);
            }
            // not exist account
            // create new account
            else
            {
				AccountDb newAccount = new AccountDb() { AccountName = loginPacket.UniqueId };
				db.Accounts.Add(newAccount);
				db.SaveChanges();

				S_Login loginOk = new S_Login() { LoginOk = 1 };
				clientSession.Send(loginOk);
			}
        }
	}
}

{% endhighlight %}

* Client\Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
    ...
	public static void S_ConnectedHandler(PacketSession session, IMessage packet)
	{
		Debug.Log("S_ConnectedHandler");
		C_Login loginPacket = new C_Login();
        // Use Unique Identifier
		loginPacket.UniqueId = SystemInfo.deviceUniqueIdentifier;
		Managers.Network.Send(loginPacket);
	}

	public static void S_LoginHandler(PacketSession session, IMessage packet)
	{
		S_Login loginPacket = (S_Login)packet;
		Debug.Log($"LoginOk({loginPacket.LoginOk})");
	}
}
{% endhighlight %}

### Test

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/6.jpg"><img src="/assets/img/posts/unity_mmocontents/6.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/7.jpg"><img src="/assets/img/posts/unity_mmocontents/7.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

[Download](https://github.com/leehuhlee/Unity){: .btn}
