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

## Player Link

### Server

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
  C_ENTER_GAME = 13;
  C_CREATE_PLAYER = 14;
  S_CREATE_PLAYER = 15;
}
...

enum PlayerServerState {
  SERVER_STATE_LOGIN = 0;
  SERVER_STATE_LOBBY = 1;
  SERVER_STATE_GAME = 2;
}
...

message S_Login{
  int32 loginOk = 1;
  repeated LobbyPlayerInfo players = 2;
}

message C_CreatePlayer{
  string name = 1;
}

message S_CreatePlayer{
  LobbyPlayerInfo player = 1;
}

message C_EnterGame{
  string name = 1;
}

message LobbyPlayerInfo{
  string name = 1;
  StatInfo statInfo = 2;
}
...
{% endhighlight %}


* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void C_MoveHandler(PacketSession session, IMessage packet)
	{
		C_Move movePacket = packet as C_Move;
		ClientSession clientSession = session as ClientSession;

		Player player = clientSession.MyPlayer;
		if (player == null)
			return;

		GameRoom room = player.Room;
		if (room == null)
			return;

		room.Push(room.HandleMove, player, movePacket);
	}

	public static void C_SkillHandler(PacketSession session, IMessage packet)
	{
		C_Skill skillPacket = packet as C_Skill;
		ClientSession clientSession = session as ClientSession;

		Player player = clientSession.MyPlayer;
		if (player == null)
			return;

		GameRoom room = player.Room;
		if (room == null)
			return;

		room.Push(room.HandleSkill, player, skillPacket);
	}

	public static void C_LoginHandler(PacketSession session, IMessage packet)
	{
		C_Login loginPacket = packet as C_Login;
		ClientSession clientSession = session as ClientSession;
		clientSession.HandleLogin(loginPacket);
	}

	public static void C_EnterGameHandler(PacketSession session, IMessage packet)
	{
		C_EnterGame enterGamePacket = (C_EnterGame)packet;
		ClientSession clientSession = (ClientSession)session;
		clientSession.HandleEnterGame(enterGamePacket);
	}

	public static void C_CreatePlayerHandler(PacketSession session, IMessage packet)
	{
		C_CreatePlayer createPlayerPacket = (C_CreatePlayer)packet;
		ClientSession clientSession = (ClientSession)session;
		clientSession.HandleCreatePlayer(createPlayerPacket);
	}
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  public partial class ClientSession : PacketSession
	{
		public PlayerServerState ServerState { get; private set; } = PlayerServerState.ServerStateLogin;

		public Player MyPlayer { get; set; }
		public int SessionId { get; set; }
		
		#region Network
		public void Send(IMessage packet)
		{
			string msgName = packet.Descriptor.Name.Replace("_", string.Empty);
			MsgId msgId = (MsgId)Enum.Parse(typeof(MsgId), msgName);
			ushort size = (ushort)packet.CalculateSize();
			byte[] sendBuffer = new byte[size + 4];
			Array.Copy(BitConverter.GetBytes((ushort)(size + 4)), 0, sendBuffer, 0, sizeof(ushort));
			Array.Copy(BitConverter.GetBytes((ushort)msgId), 0, sendBuffer, 2, sizeof(ushort));
			Array.Copy(packet.ToByteArray(), 0, sendBuffer, 4, size);
			Send(new ArraySegment<byte>(sendBuffer));
		}

		public override void OnConnected(EndPoint endPoint)
		{
			Console.WriteLine($"OnConnected : {endPoint}");

			{
				S_Connected connectedPacket = new S_Connected();
				Send(connectedPacket);
			}
		}

		public override void OnRecvPacket(ArraySegment<byte> buffer)
		{
			PacketManager.Instance.OnRecvPacket(this, buffer);
		}

		public override void OnDisconnected(EndPoint endPoint)
		{
			GameRoom room = RoomManager.Instance.Find(1);
			room.Push(room.LeaveGame, MyPlayer.Info.ObjectId);

			SessionManager.Instance.Remove(this);

			Console.WriteLine($"OnDisconnected : {endPoint}");
		}

		public override void OnSend(int numOfBytes) { }
		#endregion
	}
}
{% endhighlight %}

* Server\Session\ClientSession_PreGame.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  public int AccountDbId { get; private set; }
  public List<LobbyPlayerInfo> LobbyPlayers { get; set; } = new List<LobbyPlayerInfo>();

  public void HandleLogin(C_Login loginPacket)
  {
    if (ServerState != PlayerServerState.ServerStateLogin)
      return;

    LobbyPlayers.Clear();

    using (AppDbContext db = new AppDbContext())
    {
      AccountDb findAccount = db.Accounts
        .Include(a => a.Players)
        .Where(a => a.AccountName == loginPacket.UniqueId).FirstOrDefault();

      if (findAccount != null)
      {
        AccountDbId = findAccount.AccountDbId;

        S_Login loginOk = new S_Login() { LoginOk = 1 };
        foreach (PlayerDb playerDb in findAccount.Players)
        {
          LobbyPlayerInfo lobbyPlayer = new LobbyPlayerInfo()
          {
            Name = playerDb.PlayerName,
            StatInfo = new StatInfo()
            {
              Level = playerDb.Level,
              Hp = playerDb.Hp,
              MaxHp = playerDb.MaxHp,
              Attack = playerDb.Attack,
              Speed = playerDb.Speed,
              TotalExp = playerDb.TotalExp
            }
          };

          // Add in memory
          LobbyPlayers.Add(lobbyPlayer);

          // Add in packet
          loginOk.Players.Add(lobbyPlayer);
        }

        Send(loginOk);
        // move to Lobby
        ServerState = PlayerServerState.ServerStateLobby;
      }
      else
      {
        AccountDb newAccount = new AccountDb() { AccountName = loginPacket.UniqueId };
        db.Accounts.Add(newAccount);
        db.SaveChanges(); // TODO : Exception 

        // memory AccountDbId
        AccountDbId = newAccount.AccountDbId;

        S_Login loginOk = new S_Login() { LoginOk = 1 };
        Send(loginOk);
        // Move to Lobby
        ServerState = PlayerServerState.ServerStateLobby;
      }
    }
  }

  public void HandleEnterGame(C_EnterGame enterGamePacket)
  {
    if (ServerState != PlayerServerState.ServerStateLobby)
      return;

    LobbyPlayerInfo playerInfo = LobbyPlayers.Find(p => p.Name == enterGamePacket.Name);
    if (playerInfo == null)
      return;

    MyPlayer = ObjectManager.Instance.Add<Player>();
    {
      MyPlayer.Info.Name = playerInfo.Name;
      MyPlayer.Info.PosInfo.State = CreatureState.Idle;
      MyPlayer.Info.PosInfo.MoveDir = MoveDir.Down;
      MyPlayer.Info.PosInfo.PosX = 0;
      MyPlayer.Info.PosInfo.PosY = 0;
      MyPlayer.Stat.MergeFrom(playerInfo.StatInfo);
      MyPlayer.Session = this;
    }

    ServerState = PlayerServerState.ServerStateGame;

    GameRoom room = RoomManager.Instance.Find(1);
    room.Push(room.EnterGame, MyPlayer);
  }

  public void HandleCreatePlayer(C_CreatePlayer createPacket)
  {
    if (ServerState != PlayerServerState.ServerStateLobby)
      return;

    using (AppDbContext db = new AppDbContext())
    {
      PlayerDb findPlayer = db.Players
        .Where(p => p.PlayerName == createPacket.Name).FirstOrDefault();

      if (findPlayer != null)
      {
        // Exist name
        Send(new S_CreatePlayer());
      }
      else
      {
        // Extract 1 Level Stat Info
        StatInfo stat = null;
        DataManager.StatDict.TryGetValue(1, out stat);

        // Create Player in DB
        PlayerDb newPlayerDb = new PlayerDb()
        {
          PlayerName = createPacket.Name,
          Level = stat.Level,
          Hp = stat.Hp,
          MaxHp = stat.MaxHp,
          Attack = stat.Attack,
          Speed = stat.Speed,
          TotalExp = 0,
          AccountDbId = AccountDbId
        };

        db.Players.Add(newPlayerDb);
        db.SaveChanges(); // TODO : ExceptionHandling

        // Add in memory
        LobbyPlayerInfo lobbyPlayer = new LobbyPlayerInfo()
        {
          Name = createPacket.Name,
          StatInfo = new StatInfo()
          {
            Level = stat.Level,
            Hp = stat.Hp,
            MaxHp = stat.MaxHp,
            Attack = stat.Attack,
            Speed = stat.Speed,
            TotalExp = 0
          }
        };

        LobbyPlayers.Add(lobbyPlayer);

        // Send to Client
        S_CreatePlayer newPlayer = new S_CreatePlayer() { Player = new LobbyPlayerInfo() };
        newPlayer.Player.MergeFrom(lobbyPlayer);

        Send(newPlayer);
      }
    }
  }
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

		[ForeignKey("Account")]
		public int AccountDbId { get; set; }
		public AccountDb Account { get; set; }

		public int Level { get; set; }
		public int Hp { get; set; }
		public int MaxHp { get; set; }
		public int Attack { get; set; }
		public float Speed { get; set; }
		public int TotalExp { get; set; }
	}
{% endhighlight %}

* Package Manager Console

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/8.jpg"><img src="/assets/img/posts/unity_mmocontents/8.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/9.jpg"><img src="/assets/img/posts/unity_mmocontents/9.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

### Client

* Client\Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	public static void S_EnterGameHandler(PacketSession session, IMessage packet)
	{
		S_EnterGame enterGamePacket = packet as S_EnterGame;
		Managers.Object.Add(enterGamePacket.Player, myPlayer: true);
	}

	public static void S_LeaveGameHandler(PacketSession session, IMessage packet)
	{
		S_LeaveGame leaveGameHandler = packet as S_LeaveGame;
		Managers.Object.Clear();
	}

	public static void S_SpawnHandler(PacketSession session, IMessage packet)
	{
		S_Spawn spawnPacket = packet as S_Spawn;
		foreach (ObjectInfo obj in spawnPacket.Objects)
		{
			Managers.Object.Add(obj, myPlayer: false);
		}
	}

	public static void S_DespawnHandler(PacketSession session, IMessage packet)
	{
		S_Despawn despawnPacket = packet as S_Despawn;
		foreach (int id in despawnPacket.ObjectIds)
		{
			Managers.Object.Remove(id);
		}
	}

	public static void S_MoveHandler(PacketSession session, IMessage packet)
	{
		S_Move movePacket = packet as S_Move;

		GameObject go = Managers.Object.FindById(movePacket.ObjectId);
		if (go == null)
			return;

		if (Managers.Object.MyPlayer.Id == movePacket.ObjectId)
			return;

		BaseController bc = go.GetComponent<BaseController>();
		if (bc == null)
			return;

		bc.PosInfo = movePacket.PosInfo;
	}

	public static void S_SkillHandler(PacketSession session, IMessage packet)
	{
		S_Skill skillPacket = packet as S_Skill;

		GameObject go = Managers.Object.FindById(skillPacket.ObjectId);
		if (go == null)
			return;

		CreatureController cc = go.GetComponent<CreatureController>();
		if (cc != null)
		{
			cc.UseSkill(skillPacket.Info.SkillId);
		}
	}

	public static void S_ChangeHpHandler(PacketSession session, IMessage packet)
	{
		S_ChangeHp changePacket = packet as S_ChangeHp;

		GameObject go = Managers.Object.FindById(changePacket.ObjectId);
		if (go == null)
			return;

		CreatureController cc = go.GetComponent<CreatureController>();
		if (cc != null)
		{
			cc.Hp = changePacket.Hp;
		}
	}

	public static void S_DieHandler(PacketSession session, IMessage packet)
	{
		S_Die diePacket = packet as S_Die;

		GameObject go = Managers.Object.FindById(diePacket.ObjectId);
		if (go == null)
			return;

		CreatureController cc = go.GetComponent<CreatureController>();
		if (cc != null)
		{
			cc.Hp = 0;
			cc.OnDead();
		}
	}

	public static void S_ConnectedHandler(PacketSession session, IMessage packet)
	{
		Debug.Log("S_ConnectedHandler");
		C_Login loginPacket = new C_Login();
		loginPacket.UniqueId = SystemInfo.deviceUniqueIdentifier;
		Managers.Network.Send(loginPacket);
	}

	// Login OK + Character List
	public static void S_LoginHandler(PacketSession session, IMessage packet)
	{
		S_Login loginPacket = (S_Login)packet;
		Debug.Log($"LoginOk({loginPacket.LoginOk})");

		// TODO : Show character on Lobby UI and Select
		if (loginPacket.Players == null || loginPacket.Players.Count == 0)
		{
			C_CreatePlayer createPacket = new C_CreatePlayer();
			createPacket.Name = $"Player_{Random.Range(0, 10000).ToString("0000")}";
			Managers.Network.Send(createPacket);
		}
		else
		{
			// First Login
			LobbyPlayerInfo info = loginPacket.Players[0];
			C_EnterGame enterGamePacket = new C_EnterGame();
			enterGamePacket.Name = info.Name;
			Managers.Network.Send(enterGamePacket);
		}
	}

	public static void S_CreatePlayerHandler(PacketSession session, IMessage packet)
	{
		S_CreatePlayer createOkPacket = (S_CreatePlayer)packet;

		if (createOkPacket.Player == null)
		{
			C_CreatePlayer createPacket = new C_CreatePlayer();
			createPacket.Name = $"Player_{Random.Range(0, 10000).ToString("0000")}";
			Managers.Network.Send(createPacket);
		}
		else
		{
			C_EnterGame enterGamePacket = new C_EnterGame();
			enterGamePacket.Name = createOkPacket.Player.Name;
			Managers.Network.Send(enterGamePacket);
		}
	}
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-PlayerLink.mp4" frameborder="0"> </iframe>

## Hp Link

* Common\protoc-3.12.3-win64\bin\Protocol.proto
{% highlight proto %}
...
message LobbyPlayerInfo {
  int32 playerDbId = 1;
  string name = 2;
  StatInfo statInfo = 3;
}
...
{% endhighlight %}

* Server\Game\Object\Player.cs
{% highlight C# %}
public class Player : GameObject
{
  public int PlayerDbId { get; set; }
  public ClientSession Session { get; set; }

  public Player()
  {
    ObjectType = GameObjectType.Player;
  }

  public override void OnDamaged(GameObject attacker, int damage)
  {
    base.OnDamaged(attacker, damage);
  }

  public override void OnDead(GameObject attacker)
  {
    base.OnDead(attacker);
  }

  public void OnLeaveGame()
  {
    // TODO
    // DB Link
    // -- Do you need to access on DB when Hp is decressed?
    // 1) unsaved data will be removed
    // 2) block code flow 
    // - Async?
    // - use Another Thread?
    // -- there are some case data is continued from previous thread
    // -- create Item

    DbTransaction.SavePlayerStatus_Step1(this, Room);
  }
}
{% endhighlight %}

* Server\Session\ClientSession_PreGame.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  public int AccountDbId { get; private set; }
  public List<LobbyPlayerInfo> LobbyPlayers { get; set; } = new List<LobbyPlayerInfo>();

  public void HandleLogin(C_Login loginPacket)
  {
    if (ServerState != PlayerServerState.ServerStateLogin)
      return;

    LobbyPlayers.Clear();

    using (AppDbContext db = new AppDbContext())
    {
      AccountDb findAccount = db.Accounts
        .Include(a => a.Players)
        .Where(a => a.AccountName == loginPacket.UniqueId).FirstOrDefault();

      if (findAccount != null)
      {
        AccountDbId = findAccount.AccountDbId;

        S_Login loginOk = new S_Login() { LoginOk = 1 };
        foreach (PlayerDb playerDb in findAccount.Players)
        {
          LobbyPlayerInfo lobbyPlayer = new LobbyPlayerInfo()
          {
            PlayerDbId = playerDb.PlayerDbId,
            Name = playerDb.PlayerName,
            StatInfo = new StatInfo()
            {
              Level = playerDb.Level,
              Hp = playerDb.Hp,
              MaxHp = playerDb.MaxHp,
              Attack = playerDb.Attack,
              Speed = playerDb.Speed,
              TotalExp = playerDb.TotalExp
            }
          };

          LobbyPlayers.Add(lobbyPlayer);

          loginOk.Players.Add(lobbyPlayer);
        }

        Send(loginOk);
        ServerState = PlayerServerState.ServerStateLobby;
      }
      else
      {
        AccountDb newAccount = new AccountDb() { AccountName = loginPacket.UniqueId };
        db.Accounts.Add(newAccount);
        bool success = db.SaveChangesEx();
        if (success == false)
          return;

        AccountDbId = newAccount.AccountDbId;

        S_Login loginOk = new S_Login() { LoginOk = 1 };
        Send(loginOk);
        ServerState = PlayerServerState.ServerStateLobby;
      }
    }
  }

  public void HandleEnterGame(C_EnterGame enterGamePacket)
  {
    if (ServerState != PlayerServerState.ServerStateLobby)
      return;

    LobbyPlayerInfo playerInfo = LobbyPlayers.Find(p => p.Name == enterGamePacket.Name);
    if (playerInfo == null)
      return;

    MyPlayer = ObjectManager.Instance.Add<Player>();
    {
      MyPlayer.PlayerDbId = playerInfo.PlayerDbId;
      MyPlayer.Info.Name = playerInfo.Name;
      MyPlayer.Info.PosInfo.State = CreatureState.Idle;
      MyPlayer.Info.PosInfo.MoveDir = MoveDir.Down;
      MyPlayer.Info.PosInfo.PosX = 0;
      MyPlayer.Info.PosInfo.PosY = 0;
      MyPlayer.Stat.MergeFrom(playerInfo.StatInfo);
      MyPlayer.Session = this;
    }

    ServerState = PlayerServerState.ServerStateGame;

    GameRoom room = RoomManager.Instance.Find(1);
    room.Push(room.EnterGame, MyPlayer);
  }

  public void HandleCreatePlayer(C_CreatePlayer createPacket)
  {
    if (ServerState != PlayerServerState.ServerStateLobby)
      return;

    using (AppDbContext db = new AppDbContext())
    {
      PlayerDb findPlayer = db.Players
        .Where(p => p.PlayerName == createPacket.Name).FirstOrDefault();

      if (findPlayer != null)
      {
        Send(new S_CreatePlayer());
      }
      else
      {
        StatInfo stat = null;
        DataManager.StatDict.TryGetValue(1, out stat);

        PlayerDb newPlayerDb = new PlayerDb()
        {
          PlayerName = createPacket.Name,
          Level = stat.Level,
          Hp = stat.Hp,
          MaxHp = stat.MaxHp,
          Attack = stat.Attack,
          Speed = stat.Speed,
          TotalExp = 0,
          AccountDbId = AccountDbId
        };

        db.Players.Add(newPlayerDb);
        bool success = db.SaveChangesEx();
        if (success == false)
          return;

        LobbyPlayerInfo lobbyPlayer = new LobbyPlayerInfo()
        {
          PlayerDbId = newPlayerDb.PlayerDbId,
          Name = createPacket.Name,
          StatInfo = new StatInfo()
          {
            Level = stat.Level,
            Hp = stat.Hp,
            MaxHp = stat.MaxHp,
            Attack = stat.Attack,
            Speed = stat.Speed,
            TotalExp = 0
          }
        };

        LobbyPlayers.Add(lobbyPlayer);

        S_CreatePlayer newPlayer = new S_CreatePlayer() { Player = new LobbyPlayerInfo() };
        newPlayer.Player.MergeFrom(lobbyPlayer);

        Send(newPlayer);
      }
    }
  }
}
{% endhighlight %}

* Sever\DB\DbTransaction.cs
{% highlight C# %}
public class DbTransaction : JobSerializer
{
  public static DbTransaction Instance { get; } = new DbTransaction();

  // Me (GameRoom) -> You (Db) -> Me (GameRoom)
  public static void SavePlayerStatus_AllInOne(Player player, GameRoom room)
  {
    if (player == null || room == null)
      return;

    // Me (GameRoom)
    PlayerDb playerDb = new PlayerDb();
    playerDb.PlayerDbId = player.PlayerDbId;
    playerDb.Hp = player.Stat.Hp;

    // You
    Instance.Push(() =>
    {
      using (AppDbContext db = new AppDbContext())
      {
        db.Entry(playerDb).State = EntityState.Unchanged;
        db.Entry(playerDb).Property(nameof(PlayerDb.Hp)).IsModified = true;
        bool success = db.SaveChangesEx();
        if (success)
        {
          // Me
          room.Push(() => Console.WriteLine($"Hp Saved({playerDb.Hp})"));
        }
      }
    });			
  }

  // Me (GameRoom)
  public static void SavePlayerStatus_Step1(Player player, GameRoom room)
  {
    if (player == null || room == null)
      return;

    // Me (GameRoom)
    PlayerDb playerDb = new PlayerDb();
    playerDb.PlayerDbId = player.PlayerDbId;
    playerDb.Hp = player.Stat.Hp;
    Instance.Push<PlayerDb, GameRoom>(SavePlayerStatus_Step2, playerDb, room);
  }

  // You (Db)
  public static void SavePlayerStatus_Step2(PlayerDb playerDb, GameRoom room)
  {
    using (AppDbContext db = new AppDbContext())
    {
      db.Entry(playerDb).State = EntityState.Unchanged;
      db.Entry(playerDb).Property(nameof(PlayerDb.Hp)).IsModified = true;
      bool success = db.SaveChangesEx();
      if (success)
      {
        room.Push(SavePlayerStatus_Step3, playerDb.Hp);
      }
    }
  }

  // Me
  public static void SavePlayerStatus_Step3(int hp)
  {
    Console.WriteLine($"Hp Saved({hp})");
  }
}
{% endhighlight %}

* Server\Utils\Extensions.cs
{% highlight C# %}
public static class Extensions
{
  public static bool SaveChangesEx(this AppDbContext db)
  {
    try
    {
      db.SaveChanges();
      return true;
    }
    catch
    {
      return false;
    }
  }
}
{% endhighlight %}

### Test

* Server\Program.cs
{% highlight C# %}
class Program
{
  static Listener _listener = new Listener();
  static List<System.Timers.Timer> _timers = new List<System.Timers.Timer>();

  static void TickRoom(GameRoom room, int tick = 100)
  {
    var timer = new System.Timers.Timer();
    timer.Interval = tick;
    timer.Elapsed += ((s, e) => { room.Update(); });
    timer.AutoReset = true;
    timer.Enabled = true;

    _timers.Add(timer);
  }

  static void Main(string[] args)
  {
    ConfigManager.LoadConfig();
    DataManager.LoadData();

    GameRoom room = RoomManager.Instance.Add(1);
    TickRoom(room, 50);

    // DNS (Domain Name System)
    string host = Dns.GetHostName();
    IPHostEntry ipHost = Dns.GetHostEntry(host);
    IPAddress ipAddr = ipHost.AddressList[0];
    IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

    _listener.Init(endPoint, () => { return SessionManager.Instance.Generate(); });
    Console.WriteLine("Listening...");

    // TODO
    while (true)
    {
      DbTransaction.Instance.Flush();
    }
  }
}
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Hp-Link.mp4" frameborder="0"> </iframe>

## Item Link

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
  C_ENTER_GAME = 13;
  C_CREATE_PLAYER = 14;
  S_CREATE_PLAYER = 15;
  S_ITEM_LIST = 16;
}

...
enum ItemType{
  ITEM_TYPE_NONE = 0;
  ITEM_TYPE_WEAPON = 1;
  ITEM_TYPE_ARMOR = 2;
  ITEM_TYPE_CONSUMABLE = 3;
}

enum WeaponType{
  WEAPON_TYPE_NONE = 0;
  WEAPON_TYPE_SWORD = 1;
  WEAPON_TYPE_BOW = 2;
}

enum ArmorType{
  ARMOR_TYPE_NONE = 0;
  ARMOR_TYPE_HELMET = 1;
  ARMOR_TYPE_ARMOR = 2;
  ARMOR_TYPE_BOOTS = 3;
}

enum ConsumableType{
  CONSUMABLE_TYPE_NONE = 0;
  CONSUMABLE_TYPE_POTION = 1;
}
...

message S_ItemList{
  repeated ItemInfo items = 1; 
}
...

message ItemInfo{
  int32 itemDbId = 1;
  int32 templateId = 2;
  int32 count = 3;
  int32 slot = 4;
}
{% endhighlight %}

* Server\Data\Data.Contents.cs
{% highlight C# %}
...
#region Item
  [Serializable]
	public class ItemData
	{
		public int id;
		public string name;
		public ItemType itemType;
	}

	public class WeaponData : ItemData
    {
		public WeaponType weaponType;
		public int damage;
    }

	public class ArmorData : ItemData
	{
		public ArmorType armorType;
		public int defence;
    }

	public class ConsumableData : ItemData
    {
		public ConsumableType consumableType;
		public int maxCount;
    }

	[Serializable]
	public class ItemLoader : ILoader<int, ItemData>
	{
		public List<WeaponData> weapons = new List<WeaponData>();
		public List<ArmorData> armors = new List<ArmorData>();
		public List<ConsumableData> consumables = new List<ConsumableData>();

		public Dictionary<int, ItemData> MakeDict()
		{
			Dictionary<int, ItemData> dict = new Dictionary<int, ItemData>();
			foreach (ItemData item in weapons)
            {
				item.itemType = ItemType.Weapon;
				dict.Add(item.id, item);
			}
			foreach (ItemData item in armors)
			{
				item.itemType = ItemType.Armor;
				dict.Add(item.id, item);
			}
			foreach (ItemData item in consumables)
			{
				item.itemType = ItemType.Consumable;
				dict.Add(item.id, item);
			}

			return dict;
		}
  }
#endregion
{% endhighlight %}

* Server\Data\DataManager.cs
{% highlight C# %}
...
public class DataManager
{
  public static Dictionary<int, StatInfo> StatDict { get; private set; } = new Dictionary<int, StatInfo>();
  public static Dictionary<int, Data.Skill> SkillDict { get; private set; } = new Dictionary<int, Data.Skill>();
  public static Dictionary<int, Data.ItemData> ItemDict { get; private set; } = new Dictionary<int, Data.ItemData>();

  public static void LoadData()
  {
    StatDict = LoadJson<Data.StatData, int, StatInfo>("StatData").MakeDict();
    SkillDict = LoadJson<Data.SkillData, int, Data.Skill>("SkillData").MakeDict();
    ItemDict = LoadJson<Data.ItemLoader, int, Data.ItemData>("ItemData").MakeDict();
  }
  ...
}
{% endhighlight %}

* Server\DB\AppDbContext.cs
{% highlight C# %}
public class AppDbContext : DbContext
{
  public DbSet<AccountDb> Accounts { get; set; }
  public DbSet<PlayerDb> Players { get; set; }
  public DbSet<ItemDb> Items { get; set; }
  ...
}
{% endhighlight %}

* Server\DB\DataModel.cs
{% highlight C# %}
...
[Table("Player")]
public class PlayerDb
{
  public int PlayerDbId { get; set; }
  public string PlayerName { get; set; }

  [ForeignKey("Account")]
  public int AccountDbId { get; set; }
  public AccountDb Account { get; set; }

  public ICollection<ItemDb> Items { get; set; }

  public int Level { get; set; }
  public int Hp { get; set; }
  public int MaxHp { get; set; }
  public int Attack { get; set; }
  public float Speed { get; set; }
  public int TotalExp { get; set; }
}

[Table("Item")]
public class ItemDb
{
  public int ItemDbId { get; set; }
  public int TemplateId { get; set; }
  public int Count { get; set; }
  public int Slot { get; set;}

  [ForeignKey("Owner")]
  public int? OwnerDbId { get; set; }
  public PlayerDb Owner { get; set; } 
}
{% endhighlight %}


* Server\Game\Item\Inventory.cs
{% highlight C# %}
public class Inventory
{
  Dictionary<int, Item> _items = new Dictionary<int, Item>();

  public void Add(Item item)
  {
      _items.Add(item.ItemDbId, item);
  }

  public Item Get(int itemDbId)
  {
      Item item = null;
      _items.TryGetValue(itemDbId, out item);
      return item;
  }

  public Item Find(Func<Item, bool> condition)
  {
      foreach(Item item in _items.Values)
      {
          if (condition.Invoke(item))
              return item;
      }

      return null;
  }
}
{% endhighlight %}

* Server\Game\Item\Item.cs
{% highlight C# %}
public class Item
{
    public ItemInfo Info { get; } = new ItemInfo();

    public int ItemDbId
    {
        get { return Info.ItemDbId; }
        set { Info.ItemDbId = value; }
    }

    public int TemplateId
    {
        get { return Info.TemplateId; }
        set { Info.TemplateId = value; }
    }

    public int Count
    {
        get { return Info.Count; }
        set { Info.Count = value; }
    }

    public ItemType ItemType { get; private set; }
    public bool Stackable { get; protected set; }

    public Item(ItemType itemType)
    {
        ItemType = itemType;
    }

    public static Item MakeItem(ItemDb itemDb)
    {
        Item item = null;

        ItemData itemData = null;
        DataManager.ItemDict.TryGetValue(itemDb.TemplateId, out itemData);
        if (itemData == null)
            return null;
        
        switch (itemData.itemType)
        {
            case ItemType.Weapon:
                item = new Weapon(itemDb.TemplateId);
                break;
            case ItemType.Armor:
                item = new Armor(itemDb.TemplateId);
                break;
            case ItemType.Consumable:
                item = new Consumable(itemDb.TemplateId);
                break;
        }

        if(item != null)
        {
            item.ItemDbId = itemDb.ItemDbId;
            item.Count = item.Count;
        }

        return item;
    }
}

public class Weapon : Item
{
    public WeaponType WeaponType { get; private set; }
    public int Damage { get; private set; }

    public Weapon(int templateId) : base(ItemType.Weapon)
    {
        Init(templateId);
    }

    void Init(int templateId)
    {
        ItemData itemData = null;
        DataManager.ItemDict.TryGetValue(templateId, out itemData);
        if (itemData.itemType != ItemType.Weapon) 
            return;

        WeaponData data = (WeaponData)itemData;
        {
            TemplateId = data.id;
            Count = 1;
            WeaponType = data.weaponType;
            Damage = data.damage;
            Stackable = false;
        }
    }
}

public class Armor : Item
{
    public ArmorType ArmorType { get; private set; }
    public int Defence { get; private set; }

    public Armor(int templateId) : base(ItemType.Armor)
    {
        Init(templateId);
    }

    void Init(int templateId)
    {
        ItemData itemData = null;
        DataManager.ItemDict.TryGetValue(templateId, out itemData);
        if (itemData.itemType != ItemType.Armor)
            return;

        ArmorData data = (ArmorData)itemData;
        {
            TemplateId = data.id;
            Count = 1;
            ArmorType = data.armorType;
            Defence = data.defence;
            Stackable = false;
        }
    }
}

public class Consumable : Item
{
    public ConsumableType ConsumableType { get; private set; }
    public int MaxCount { get; private set; }

    public Consumable(int templateId) : base(ItemType.Consumable)
    {
        Init(templateId);
    }

    void Init(int templateId)
    {
        ItemData itemData = null;
        DataManager.ItemDict.TryGetValue(templateId, out itemData);
        if (itemData.itemType != ItemType.Consumable)
            return;

        ConsumableData data = (ConsumableData)itemData;
        {
            TemplateId = data.id;
            Count = 1;
            ConsumableType = data.consumableType;
            MaxCount = data.maxCount;
            Stackable = false;
        }
    }
}
{% endhighlight %}

* Server\Object\Player.cs
{% highlight C# %}
public class Player : GameObject
{
  public int PlayerDbId { get; set; }
  public ClientSession Session { get; set; }
  public Inventory Inven { get; private set; } = new Inventory();
  ...
}
{% endhighlight %}

* Server\Session\ClientSession_PreGame.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  ...
  public void HandleEnterGame(C_EnterGame enterGamePacket)
  {
    if (ServerState != PlayerServerState.ServerStateLobby)
      return;

    LobbyPlayerInfo playerInfo = LobbyPlayers.Find(p => p.Name == enterGamePacket.Name);
    if (playerInfo == null)
      return;

    MyPlayer = ObjectManager.Instance.Add<Player>();
    {
      MyPlayer.PlayerDbId = playerInfo.PlayerDbId;
      MyPlayer.Info.Name = playerInfo.Name;
      MyPlayer.Info.PosInfo.State = CreatureState.Idle;
      MyPlayer.Info.PosInfo.MoveDir = MoveDir.Down;
      MyPlayer.Info.PosInfo.PosX = 0;
      MyPlayer.Info.PosInfo.PosY = 0;
      MyPlayer.Stat.MergeFrom(playerInfo.StatInfo);
      MyPlayer.Session = this;

      S_ItemList itemListPacket = new S_ItemList();

      // 아이템 목록을 갖고온다
      using (AppDbContext db = new AppDbContext())
              {
        List<ItemDb> items = db.Items
          .Where(i => i.OwnerDbId == playerInfo.PlayerDbId)
          .ToList();

        foreach(ItemDb itemDb in items)
                  {
          Item item = Item.MakeItem(itemDb);
          if(item != null)
                      {
            MyPlayer.Inven.Add(item);

            ItemInfo info = new ItemInfo();
            info.MergeFrom(item.Info);
            itemListPacket.Items.Add(info);
                      }
        }
              }
      Send(itemListPacket);
    }

    ServerState = PlayerServerState.ServerStateGame;

    GameRoom room = RoomManager.Instance.Find(1);
    room.Push(room.EnterGame, MyPlayer);
  }
  ...
}
{% endhighlight %}

* Package Console Manager
- add-migration Item
- update-database

* Client\Assets\Scripts\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
  ...
  public static void S_ItemListHandler(PacketSession session, IMessage packet)
  {
    S_ItemList itemList = (S_ItemList)packet;

    foreach (ItemInfo item in itemList.Items)
    {
      Debug.Log($"{item.TemplateId} : {item.Count}");
    }
  }
}
{% endhighlight %}

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

          // TEST CODE
          using (AppDbContext db = new AppDbContext())
          {
              PlayerDb player = db.Players.FirstOrDefault();
              if (player != null)
              {
                  db.Items.Add(new ItemDb()
                  {
                      TemplateId = 1,
                      Count = 1,
                      Slot = 0,
                      Owner = player
                  });

                  db.Items.Add(new ItemDb()
                  {
                      TemplateId = 100,
                      Count = 1,
                      Slot = 1,
                      Owner = player
                  });

                  db.Items.Add(new ItemDb()
                  {
                      TemplateId = 101,
                      Count = 1,
                      Slot = 2,
                      Owner = player
                  });

                  db.Items.Add(new ItemDb()
                  {
                      TemplateId = 200,
                      Count = 10,
                      Slot = 5,
                      Owner = player
                  });

                  db.SaveChanges();
              }
          }
  ...
  }
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/10.jpg"><img src="/assets/img/posts/unity_mmocontents/10.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

[Download](https://github.com/leehuhlee/Unity){: .btn}
