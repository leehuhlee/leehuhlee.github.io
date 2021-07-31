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

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Player-Link.mp4" frameborder="0"> </iframe>

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

<figure>
  <a href="/assets/img/posts/unity_mmocontents/10.jpg"><img src="/assets/img/posts/unity_mmocontents/10.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

## Inventory

### UI

* Asset Store
- RPG Inventory Icons
- Fantastic UI Starter Pack

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/11.jpg"><img src="/assets/img/posts/unity_mmocontents/11.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/12.jpg"><img src="/assets/img/posts/unity_mmocontents/12.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Arrange Folders
<figure>
  <a href="/assets/img/posts/unity_mmocontents/13.jpg"><img src="/assets/img/posts/unity_mmocontents/13.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Make UI

- UI_GameScene<
<figure>
  <a href="/assets/img/posts/unity_mmocontents/15.jpg"><img src="/assets/img/posts/unity_mmocontents/15.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

- UI_Stat
<figure>
  <a href="/assets/img/posts/unity_mmocontents/16.jpg"><img src="/assets/img/posts/unity_mmocontents/16.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

- UI_Inventory
<figure>
  <a href="/assets/img/posts/unity_mmocontents/17.jpg"><img src="/assets/img/posts/unity_mmocontents/17.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

- UI_Inventory_Item
<figure>
  <a href="/assets/img/posts/unity_mmocontents/18.jpg"><img src="/assets/img/posts/unity_mmocontents/18.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

### Client

* Client\Assets\Scripts\Data\Data.Contents.cs
{% highlight C# %}
#region Item
  [Serializable]
  public class ItemData
  {
    public int id;
    public string name;
    public ItemType itemType;
    public string iconPath;
  }

  [Serializable]
  public class WeaponData : ItemData
  {
    public WeaponType weaponType;
    public int damage;
  }

  [Serializable]
  public class ArmorData : ItemData
  {
    public ArmorType armorType;
    public int defence;
  }

  [Serializable]
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

* Client\Assets\Scripts\Managers\DataManager.cs
{% highlight C# %}
public interface ILoader<Key, Value>
{
    Dictionary<Key, Value> MakeDict();
}

public class DataManager
{
    public Dictionary<int, Data.Skill> SkillDict { get; private set; } = new Dictionary<int, Data.Skill>();
    public Dictionary<int, Data.ItemData> ItemDict { get; private set; } = new Dictionary<int, Data.ItemData>();

	public void Init()
    {
        SkillDict = LoadJson<Data.SkillData, int, Data.Skill>("SkillData").MakeDict();
        ItemDict = LoadJson<Data.ItemLoader, int, Data.ItemData>("ItemData").MakeDict();
	}

    Loader LoadJson<Loader, Key, Value>(string path) where Loader : ILoader<Key, Value>
    {
		TextAsset textAsset = Managers.Resource.Load<TextAsset>($"Data/{path}");
        return JsonUtility.FromJson<Loader>(textAsset.text);
	}
}
{% endhighlight %}

* Client\Assets\Scripts\Managers\UIManager.cs
{% highlight C# %}
public class UIManager
{
    int _order = 10;

    Stack<UI_Popup> _popupStack = new Stack<UI_Popup>();
    public UI_Scene SceneUI { get; private set; }

    public GameObject Root
    {
        get
        {
			GameObject root = GameObject.Find("@UI_Root");
			if (root == null)
				root = new GameObject { name = "@UI_Root" };
            return root;
		}
    }

    public void SetCanvas(GameObject go, bool sort = true)
    {
        Canvas canvas = Util.GetOrAddComponent<Canvas>(go);
        canvas.renderMode = RenderMode.ScreenSpaceOverlay;
        canvas.overrideSorting = true;

        if (sort)
        {
            canvas.sortingOrder = _order;
            _order++;
        }
        else
        {
            canvas.sortingOrder = 0;
        }
    }

	public T MakeWorldSpaceUI<T>(Transform parent = null, string name = null) where T : UI_Base
	{
		if (string.IsNullOrEmpty(name))
			name = typeof(T).Name;

		GameObject go = Managers.Resource.Instantiate($"UI/WorldSpace/{name}");
		if (parent != null)
			go.transform.SetParent(parent);

        Canvas canvas = go.GetOrAddComponent<Canvas>();
        canvas.renderMode = RenderMode.WorldSpace;
        canvas.worldCamera = Camera.main;

		return Util.GetOrAddComponent<T>(go);
	}

	public T MakeSubItem<T>(Transform parent = null, string name = null) where T : UI_Base
	{
		if (string.IsNullOrEmpty(name))
			name = typeof(T).Name;

		GameObject go = Managers.Resource.Instantiate($"UI/SubItem/{name}");
		if (parent != null)
			go.transform.SetParent(parent);

		return Util.GetOrAddComponent<T>(go);
	}

	public T ShowSceneUI<T>(string name = null) where T : UI_Scene
	{
		if (string.IsNullOrEmpty(name))
			name = typeof(T).Name;

		GameObject go = Managers.Resource.Instantiate($"UI/Scene/{name}");
		T sceneUI = Util.GetOrAddComponent<T>(go);
        SceneUI = sceneUI;

		go.transform.SetParent(Root.transform);

		return sceneUI;
	}

	public T ShowPopupUI<T>(string name = null) where T : UI_Popup
    {
        if (string.IsNullOrEmpty(name))
            name = typeof(T).Name;

        GameObject go = Managers.Resource.Instantiate($"UI/Popup/{name}");
        T popup = Util.GetOrAddComponent<T>(go);
        _popupStack.Push(popup);

        go.transform.SetParent(Root.transform);

		return popup;
    }

    public void ClosePopupUI(UI_Popup popup)
    {
		if (_popupStack.Count == 0)
			return;

        if (_popupStack.Peek() != popup)
        {
            Debug.Log("Close Popup Failed!");
            return;
        }

        ClosePopupUI();
    }

    public void ClosePopupUI()
    {
        if (_popupStack.Count == 0)
            return;

        UI_Popup popup = _popupStack.Pop();
        Managers.Resource.Destroy(popup.gameObject);
        popup = null;
        _order--;
    }

    public void CloseAllPopupUI()
    {
        while (_popupStack.Count > 0)
            ClosePopupUI();
    }

    public void Clear()
    {
        CloseAllPopupUI();
        SceneUI = null;
    }
}
{% endhighlight %}

* Client\Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
  ...
  public static void S_ItemListHandler(PacketSession session, IMessage packet)
  {
    S_ItemList itemList = (S_ItemList)packet;

    UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
    UI_Inventory invenUI = gameSceneUI.InvenUI;

    Managers.Inven.Clear();

    // Apply Item data in Memory
    foreach (ItemInfo itemInfo in itemList.Items)
    {
      Item item = Item.MakeItem(itemInfo);
      Managers.Inven.Add(item);
    }

    // Set UI
    invenUI.gameObject.SetActive(true);
    invenUI.RefreshUI();
  }
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scenes\GameScene.cs
{% highlight C# %}
public class GameScene : BaseScene
{
    UI_GameScene _sceneUI;

    protected override void Init()
    {
        base.Init();

        SceneType = Define.Scene.Game;

        Managers.Map.LoadMap(1);

        Screen.SetResolution(640, 480, false);

        _sceneUI = Managers.UI.ShowSceneUI<UI_GameScene>();
    }

    public override void Clear()
    {
        
    }
}
{% endhighlight %}

* Client\Assets\Scripts\UI\UI_Base.cs
{% highlight C# %}
public abstract class UI_Base : MonoBehaviour
{
	protected Dictionary<Type, UnityEngine.Object[]> _objects = new Dictionary<Type, UnityEngine.Object[]>();
	public abstract void Init();

  // Change Start to Awake
  // Because sometimes RefreshUI is running first than Init
	private void Awake()
	{
		Init();
	}
  ...
}
{% endhighlight %}

* Client\Assets\Scripts\Contents\Item.cs
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

    public int Slot
    {
        get { return Info.Slot; }
        set { Info.Slot = value; }
    }

    public ItemType ItemType { get; private set; }
    public bool Stackable { get; protected set; }

    public Item(ItemType itemType)
    {
        ItemType = itemType;
    }

    public static Item MakeItem(ItemInfo itemInfo)
    {
        Item item = null;

        ItemData itemData = null;
        Managers.Data.ItemDict.TryGetValue(itemInfo.TemplateId, out itemData);
        if (itemData == null)
            return null;

        switch (itemData.itemType)
        {
            case ItemType.Weapon:
                item = new Weapon(itemInfo.TemplateId);
                break;
            case ItemType.Armor:
                item = new Armor(itemInfo.TemplateId);
                break;
            case ItemType.Consumable:
                item = new Consumable(itemInfo.TemplateId);
                break;
        }

        if (item != null)
        {
            item.ItemDbId = itemInfo.ItemDbId;
            item.Count = itemInfo.Count;
            item.Slot = itemInfo.Slot;
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
        Managers.Data.ItemDict.TryGetValue(templateId, out itemData);
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
        Managers.Data.ItemDict.TryGetValue(templateId, out itemData);
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
        Managers.Data.ItemDict.TryGetValue(templateId, out itemData);
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

* Client\Assets\Scripts\Managers\Contents\InventoryManager.cs
{% highlight C# %}
public class InventoryManager : MonoBehaviour
{
    public Dictionary<int, Item> Items { get; } = new Dictionary<int, Item>();

    public void Add(Item item)
    {
        Items.Add(item.ItemDbId, item);
    }

    public Item Get(int itemDbId)
    {
        Item item = null;
        Items.TryGetValue(itemDbId, out item);
        return item;
    }

    public Item Find(Func<Item, bool> condition)
    {
        foreach (Item item in Items.Values)
        {
            if (condition.Invoke(item))
                return item;
        }

        return null;
    }

    public void Clear()
    {
        Items.Clear();
    }
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scene\UI_GameScene.cs
{% highlight C# %}
public class UI_GameScene : UI_Scene
{
    public UI_Stat StatUI { get; private set; }
    public UI_Inventory InvenUI { get; private set; }

    public override void Init()
    {
        base.Init();

        StatUI = GetComponentInChildren<UI_Stat>();
        InvenUI = GetComponentInChildren<UI_Inventory>();

        StatUI.gameObject.SetActive(false);
        InvenUI.gameObject.SetActive(false);

    }
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scene\UI_Inventory.cs
{% highlight C# %}
public class UI_Inventory : UI_Base
{
    public List<UI_Inventory_Item> Items { get; } = new List<UI_Inventory_Item>();

    public override void Init()
    {
        Items.Clear();

        GameObject grid = transform.Find("ItemGrid").gameObject;
        foreach (Transform child in grid.transform)
            Destroy(child.gameObject);

        for(int i = 0; i < 20; i++)
        {
            GameObject go = Managers.Resource.Instantiate("UI/Scene/UI_Inventory_Item", grid.transform);
            UI_Inventory_Item item = go.GetOrAddComponent<UI_Inventory_Item>();
            Items.Add(item);
        }
    }

    public void RefreshUI()
    {
        List<Item> items = Managers.Inven.Items.Values.ToList();
        items.Sort((left, right) => { return left.Slot - right.Slot; });

        foreach(Item item in items)
        {
            if (item.Slot < 0 || item.Slot >= 20)
                continue;

            Items[item.Slot].SetItem(item.TemplateId, item.Count);
        }
    }
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scene\UI_Inventory_Item.cs
{% highlight C# %}
public class UI_Inventory_Item : UI_Base
{
    [SerializeField]
    Image _icon;

    public override void Init()
    {

    }

    public void SetItem(int templateId, int count)
    {
        Data.ItemData itemData = null;
        Managers.Data.ItemDict.TryGetValue(templateId, out itemData);

        Sprite icon = Managers.Resource.Load<Sprite>(itemData.iconPath);
        _icon.sprite = icon;
    }
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scene\UI_Stat.cs
{% highlight C# %}
public class UI_Stat : UI_Base
{
    public override void Init()
    {

    }
}
{% endhighlight %}

### Server

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

    public int Slot
    {
        get { return Info.Slot; }
        set { Info.Slot = value; }
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
            item.Count = itemDb.Count;
            item.Slot = itemDb.Slot;
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

### Test

<figure>
  <a href="/assets/img/posts/unity_mmocontents/14.jpg"><img src="/assets/img/posts/unity_mmocontents/14.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

## Reward

### Client

* Assets\Resources\Data\MonsterData.json
{% highlight json %}
{
  "monsters": [
    {
      "id": "1",
      "name": "doppelganger",
      "stat": {
        "level": "1",
        "maxHp": "20",
        "attack": "5",
        "speed": "5.0",
        "totalExp": "10"
      },
      "rewards": [
        {
          "probability": "10",
          "itemId": "1",
          "count": "1"
        },
        {
          "probability": "10",
          "itemId": "2",
          "count": "1"
        },
        {
          "probability": "10",
          "itemId": "100",
          "count": "1"
        },
        {
          "probability": "10",
          "itemId": "101",
          "count": "1"
        },
        {
          "probability": "10",
          "itemId": "200",
          "count": "5"
        }
      ]
    }
  ]
} 
{% endhighlight %}

* Assets\Scripts\Controllers\MyPlayerController.cs
{% highlight C# %}
public class MyPlayerController : PlayerController
{
	bool _moveKeyPressed = false;

	protected override void Init()
	{
		base.Init();
	}

	protected override void UpdateController()
	{
		GetUIKeyInput();
    ...
  }
  ...

  void GetUIKeyInput()
  {
    if (Input.GetKeyDown(KeyCode.I))
    {
      UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
      UI_Inventory invenUI = gameSceneUI.InvenUI;

      if (invenUI.gameObject.activeSelf)
      {
        invenUI.gameObject.SetActive(false);
      }
      else
      {
        invenUI.gameObject.SetActive(true);
        invenUI.RefreshUI();
      }
    }
  }
  ...
}
{% endhighlight %}

* Assets\Scripts\Data\Data.Contents.cs
{% highlight C# %}
...
#region Monster
	[Serializable]
	public class MonsterData
	{
		public int id;
		public string name;
		public StatInfo stat;
		public string prefabPath;
	}

	[Serializable]
	public class MonsterLoader : ILoader<int, MonsterData>
	{
		public List<MonsterData> monsters = new List<MonsterData>();

		public Dictionary<int, MonsterData> MakeDict()
		{
			Dictionary<int, MonsterData> dict = new Dictionary<int, MonsterData>();
			foreach (MonsterData monster in monsters)
			{
				dict.Add(monster.id, monster);
			}
			return dict;
		}
	}
#endregion
{% endhighlight %}

* Assets\Scripts\Managers\Core\DataManager.cs
{% highlight C# %}
...
public class DataManager
{
  public Dictionary<int, Data.Skill> SkillDict { get; private set; } = new Dictionary<int, Data.Skill>();
  public Dictionary<int, Data.ItemData> ItemDict { get; private set; } = new Dictionary<int, Data.ItemData>();
  public Dictionary<int, Data.MonsterData> MonsterDict { get; private set; } = new Dictionary<int, Data.MonsterData>();

	public void Init()
  {
    SkillDict = LoadJson<Data.SkillData, int, Data.Skill>("SkillData").MakeDict();
    ItemDict = LoadJson<Data.ItemLoader, int, Data.ItemData>("ItemData").MakeDict();
    MonsterDict = LoadJson<Data.MonsterLoader, int, Data.MonsterData>("MonsterData").MakeDict();
	}
  ...
}
{% endhighlight %}

* Assets\Scripts\Packet\MyPlayerController.cs
{% highlight C# %}
class PacketHandler
{
  ...
  public static void S_ItemListHandler(PacketSession session, IMessage packet)
  {
		S_ItemList itemList = (S_ItemList)packet;

		UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
		UI_Inventory invenUI = gameSceneUI.InvenUI;

		Managers.Inven.Clear();

		foreach (ItemInfo itemInfo in itemList.Items)
        {
			Item item = Item.MakeItem(itemInfo);
			Managers.Inven.Add(item);
        }

		//invenUI.gameObject.SetActive(true);
		//invenUI.RefreshUI();
  }

	public static void S_AddItemHandler(PacketSession session, IMessage packet)
  {
		S_AddItem itemList = (S_AddItem)packet;

		UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
		UI_Inventory invenUI = gameSceneUI.InvenUI;

		// Apply Item data in Memory
		foreach (ItemInfo itemInfo in itemList.Items)
		{
			Item item = Item.MakeItem(itemInfo);
			Managers.Inven.Add(item);
		}

		Debug.Log("Get Item!");
	}
}
{% endhighlight %}

### Server

* Server\Data\Data.Contents.cs
{% highlight C# %}

#region Monster
	[Serializable]
	public class RewardData
  {
		public int probability; // 100분율
		public int itemId;
		public int count;
  }

	[Serializable]
	public class MonsterData
  {
		public int id;
		public string name;
		public StatInfo stat;
		public List<RewardData> rewards;
		//public string prefabPath;
  }

	[Serializable]
	public class MonsterLoader : ILoader<int, MonsterData>
	{
		public List<MonsterData> monsters = new List<MonsterData>();

		public Dictionary<int, MonsterData> MakeDict()
		{
			Dictionary<int, MonsterData> dict = new Dictionary<int, MonsterData>();
			foreach (MonsterData monster in monsters)
			{
				dict.Add(monster.id, monster);
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
  public static Dictionary<int, Data.MonsterData> MonsterDict { get; private set; } = new Dictionary<int, Data.MonsterData>();

  public static void LoadData()
  {
    StatDict = LoadJson<Data.StatData, int, StatInfo>("StatData").MakeDict();
    SkillDict = LoadJson<Data.SkillData, int, Data.Skill>("SkillData").MakeDict();
    ItemDict = LoadJson<Data.ItemLoader, int, Data.ItemData>("ItemData").MakeDict();
    MonsterDict = LoadJson<Data.MonsterLoader, int, Data.MonsterData>("MonsterData").MakeDict();
  }
  ...
}
{% endhighlight %}

* Server\DB\DbTransaction.cs
{% highlight C# %}
public class DbTransaction : JobSerializer
{
  ...

  public static void RewardPlayer(Player player, RewardData rewardData, GameRoom room)
  {
    if (player == null || rewardData == null || room == null)
      return;

    // TODO : Multi Thread Problem
    int? slot = player.Inven.GetEmptySlot();
    if (slot == null)
      return;

    ItemDb itemDb = new ItemDb()
    {
      TemplateId = rewardData.itemId,
      Count = rewardData.count,
      Slot = slot.Value,
      OwnerDbId = player.PlayerDbId
    };

    // You
    Instance.Push(() =>
    {
      using (AppDbContext db = new AppDbContext())
      {
        db.Items.Add(itemDb);
        bool success = db.SaveChangesEx();
        if (success)
        {
          // Me
          room.Push(() =>
          {
            Item newItem = Item.MakeItem(itemDb);
            player.Inven.Add(newItem);

            // TODO :Client Noti
            {
              S_AddItem ItemPacket = new S_AddItem();
              ItemInfo itemInfo = new ItemInfo();
              itemInfo.MergeFrom(newItem.Info);
              ItemPacket.Items.Add(itemInfo);

              player.Session.Send(ItemPacket);
            }
          });
        }
      }
    });
  }
}
{% endhighlight %}

* Server\Game\Item\Inventory.cs
{% highlight C# %}
public class Inventory
{
  ...
  public int? GetEmptySlot()
  {
      for(int slot = 0; slot <20; slot++)
      {
          Item item = _items.Values.FirstOrDefault(i => i.Slot == slot);
          if (item == null)
              return slot;
      }
      return null;
  }
}
{% endhighlight %}

* Server\Game\Object\Arrow.cs
{% highlight C# %}
public class Arrow : Projectile
{
  ...
  public override GameObject GetOwner()
  {
    return Owner;
  }
}
{% endhighlight %}

* Server\Game\Object\GameObject.cs
{% highlight C# %}
public class GameObject
{
  ...
  public virtual GameObject GetOwner()
  {
    return this;
  }
}
{% endhighlight %}

* Server\Game\Object\Monster.cs
{% highlight C# %}
public class Monster : GameObject
{
  public int TemplateId { get; private set; }

  public Monster()
  {
    ObjectType = GameObjectType.Monster;
  }

  public void Init(int templateId)
  {
    TemplateId = templateId;

    MonsterData monsterData = null;
    DataManager.MonsterDict.TryGetValue(TemplateId, out monsterData);
    Stat.MergeFrom(monsterData.stat);
    Stat.Hp = monsterData.stat.MaxHp;
    State = CreatureState.Idle;
  }
  ...

  public override void OnDead(GameObject attacker)
  {
    base.OnDead(attacker);

    GameObject owner = attacker.GetOwner();
    if(owner.ObjectType == GameObjectType.Player)
    {
      RewardData rewardData = GetRendomReward();
      if(rewardData != null)
      {
        Player player = (Player)owner;
        DbTransaction.RewardPlayer(player, rewardData, Room);
      }
    }
  }

  RewardData GetRendomReward()
  {
    MonsterData monsterData = null;
    DataManager.MonsterDict.TryGetValue(TemplateId, out monsterData);
			
    int rand = new Random().Next(0,101);

    int sum = 0;
    foreach (RewardData rewardData in monsterData.rewards)
    {
      sum += rewardData.probability;
      if(rand <= sum)
      {
        return rewardData;
      }
    }

    return null;
  }
}
{% endhighlight %}

* Server\Game\Room\GameRoom.cs
{% highlight C# %}
public class GameRoom : JobSerializer
{
	...
  public void Init(int mapId)
  {
    Map.LoadMap(mapId);

    // TEMP
    Monster monster = ObjectManager.Instance.Add<Monster>();
    monster.Init(1);
    ...
  }
  ...
}
{% endhighlight %}

### Test

* Delete Item List

<figure>
  <a href="/assets/img/posts/unity_mmocontents/19.jpg"><img src="/assets/img/posts/unity_mmocontents/19.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Reward.mp4" frameborder="0"> </iframe>

## Item Using

### Client

* Client\Assets\Scripts\Contents\Item.cs
{% highlight C# %}
public class Item
{
  ...
	public bool Equipped
	{
		get { return Info.Equipped; }
		set { Info.Equipped = value; }
	}
  ...

  public static Item MakeItem(ItemInfo itemInfo)
	{
		Item item = null;

		ItemData itemData = null;
		Managers.Data.ItemDict.TryGetValue(itemInfo.TemplateId, out itemData);

		if (itemData == null)
			return null;

		switch (itemData.itemType)
		{
			case ItemType.Weapon:
				item = new Weapon(itemInfo.TemplateId);
				break;
			case ItemType.Armor:
				item = new Armor(itemInfo.TemplateId);
				break;
			case ItemType.Consumable:
				item = new Consumable(itemInfo.TemplateId);
				break;
		}

		if (item != null)
		{
			item.ItemDbId = itemInfo.ItemDbId;
			item.Count = itemInfo.Count;
			item.Slot = itemInfo.Slot;
			item.Equipped = itemInfo.Equipped;
		}

		return item;
	}
}
...

public class Consumable : Item
{
	public ConsumableType ConsumableType { get; private set; }
	public int MaxCount { get; set; }
	...

	void Init(int templateId)
	{
		ItemData itemData = null;
		Managers.Data.ItemDict.TryGetValue(templateId, out itemData);
		if (itemData.itemType != ItemType.Consumable)
			return;

		ConsumableData data = (ConsumableData)itemData;
		{
			TemplateId = data.id;
			Count = 1;
			MaxCount = data.maxCount;
			ConsumableType = data.consumableType;
			Stackable = (data.maxCount > 1);
		}
	}
}
{% endhighlight %}

* Client\Assets\Scripts\Controllers\MyPlayerController.cs
{% highlight C# %}
public class MyPlayerController : PlayerController
{
	bool _moveKeyPressed = false;

	public int WeaponDamage { get; private set; }
	public int ArmorDefence { get; private set; }

	protected override void Init()
	{
		base.Init();
		RefreshAdditionalStat();
	}
  ...

  public void RefreshAdditionalStat()
	{
		WeaponDamage = 0;
		ArmorDefence = 0;

		foreach (Item item in Managers.Inven.Items.Values)
		{
			if (item.Equipped == false)
				continue;

			switch (item.ItemType)
			{
				case ItemType.Weapon:
					WeaponDamage += ((Weapon)item).Damage;
					break;
				case ItemType.Armor:
					ArmorDefence += ((Armor)item).Defence;
					break;
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
  public static void S_ItemListHandler(PacketSession session, IMessage packet)
	{
		S_ItemList itemList = (S_ItemList)packet;

		Managers.Inven.Clear();

		// 메모리에 아이템 정보 적용
		foreach (ItemInfo itemInfo in itemList.Items)
		{
			Item item = Item.MakeItem(itemInfo);
			Managers.Inven.Add(item);
		}

		if (Managers.Object.MyPlayer != null)
			Managers.Object.MyPlayer.RefreshAdditionalStat();
	}

	public static void S_AddItemHandler(PacketSession session, IMessage packet)
	{
		S_AddItem itemList = (S_AddItem)packet;

		// Apply Item data in memory
		foreach (ItemInfo itemInfo in itemList.Items)
		{
			Item item = Item.MakeItem(itemInfo);
			Managers.Inven.Add(item);
		}

		Debug.Log("Get Item!");

		UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
		UI_Inventory invenUI = gameSceneUI.InvenUI;
		invenUI.RefreshUI();

		if (Managers.Object.MyPlayer != null)
			Managers.Object.MyPlayer.RefreshAdditionalStat();
	}

	public static void S_EquipItemHandler(PacketSession session, IMessage packet)
	{
		S_EquipItem equipItemOk = (S_EquipItem)packet;

		// Apply Item data in memory
		Item item = Managers.Inven.Get(equipItemOk.ItemDbId);
		if (item == null)
			return;

		item.Equipped = equipItemOk.Equipped;
		Debug.Log("Change setted item!");

		UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
		UI_Inventory invenUI = gameSceneUI.InvenUI;
		invenUI.RefreshUI();

		if (Managers.Object.MyPlayer != null)
			Managers.Object.MyPlayer.RefreshAdditionalStat();
	}

	public static void S_ChangeStatHandler(PacketSession session, IMessage packet)
	{
		S_ChangeStat itemList = (S_ChangeStat)packet;

		// TODO
	}
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scene\UI_Inventory.cs
{% highlight C# %}
public class UI_Inventory : UI_Base
{
	public List<UI_Inventory_Item> Items { get; } = new List<UI_Inventory_Item>();

	public override void Init()
	{
		Items.Clear();

		GameObject grid = transform.Find("ItemGrid").gameObject;
		foreach (Transform child in grid.transform)
			Destroy(child.gameObject);

		for (int i = 0; i < 20; i++)
		{
			GameObject go = Managers.Resource.Instantiate("UI/Scene/UI_Inventory_Item", grid.transform);
			UI_Inventory_Item item = go.GetOrAddComponent<UI_Inventory_Item>();
			Items.Add(item);
		}

		RefreshUI();
	}

	public void RefreshUI()
	{
		if (Items.Count == 0)
			return;

		List<Item> items = Managers.Inven.Items.Values.ToList();
		items.Sort((left, right) => { return left.Slot - right.Slot; });

		foreach (Item item in items)
		{
			if (item.Slot < 0 || item.Slot >= 20)
				continue;

			Items[item.Slot].SetItem(item);
		}
	}
}
{% endhighlight %}

* Client\Assets\Scripts\UI\Scene\UI_Inventory_Item.cs
{% highlight C# %}
public class UI_Inventory_Item : UI_Base
{
	[SerializeField]
	Image _icon = null;

	[SerializeField]
	Image _frame = null;

	public int ItemDbId { get; private set; }
	public int TemplateId { get; private set; }
	public int Count { get; private set; }
	public bool Equipped { get; private set; }

	public override void Init()
	{
		_icon.gameObject.BindEvent((e) =>
		{
			Debug.Log("Click Item");

			Data.ItemData itemData = null;
			Managers.Data.ItemDict.TryGetValue(TemplateId, out itemData);

			// TODO : C_USE_ITEM 아이템 사용 패킷
			if (itemData.itemType == ItemType.Consumable)
				return;

			C_EquipItem equipPacket = new C_EquipItem();
			equipPacket.ItemDbId = ItemDbId;
			equipPacket.Equipped = !Equipped;

			Managers.Network.Send(equipPacket);
		});
	}

	public void SetItem(Item item)
	{
		ItemDbId = item.ItemDbId;
		TemplateId = item.TemplateId;
		Count = item.Count;
		Equipped = item.Equipped;

		Data.ItemData itemData = null;
		Managers.Data.ItemDict.TryGetValue(TemplateId, out itemData);

		Sprite icon = Managers.Resource.Load<Sprite>(itemData.iconPath);
		_icon.sprite = icon;

		_frame.gameObject.SetActive(Equipped);
	}
}
{% endhighlight %}

### Server

* Server\DB\DataModel.cs
{% highlight C# %}
[Table("Item")]
public class ItemDb
{
  public int ItemDbId { get; set; }
  public int TemplateId { get; set; }
  public int Count { get; set; }
  public int Slot { get; set; }
  public bool Equipped { get; set; } = false;

  [ForeignKey("Owner")]
  public int? OwnerDbId { get; set; }
  public PlayerDb Owner { get; set; }
}
{% endhighlight %}

* Server\DB\DbTransaction.cs
{% highlight C# %}
public partial class DbTransaction : JobSerializer
{

    ...
    public static void RewardPlayer(Player player, RewardData rewardData, GameRoom room)
		{
			if (player == null || rewardData == null || room == null)
				return;

			// TODO : multi thread problem
			// 1) Request to save on DB
			// 2) DB Save OK
			// 3) Apply on Memory
			int? slot = player.Inven.GetEmptySlot();
			if (slot == null)
				return;

			ItemDb itemDb = new ItemDb()
			{
				TemplateId = rewardData.itemId,
				Count = rewardData.count,
				Slot = slot.Value,
				OwnerDbId = player.PlayerDbId
			};

			// You
			Instance.Push(() =>
			{
				using (AppDbContext db = new AppDbContext())
				{
					db.Items.Add(itemDb);
					bool success = db.SaveChangesEx();
					if (success)
					{
						// Me
						room.Push(() =>
						{
							Item newItem = Item.MakeItem(itemDb);
							player.Inven.Add(newItem);

							// Client Noti
							{
								S_AddItem itemPacket = new S_AddItem();
								ItemInfo itemInfo = new ItemInfo();
								itemInfo.MergeFrom(newItem.Info);
								itemPacket.Items.Add(itemInfo);

								player.Session.Send(itemPacket);
							}
						});
					}
				}
			});
		}
	}
}
{% endhighlight %}

* Server\DB\DbTransaction_Noti.cs
{% highlight C# %}
public partial class DbTransaction : JobSerializer
	{
		public static void EquipItemNoti(Player player, Item item)
		{
			if (player == null || item == null)
				return;

			ItemDb itemDb = new ItemDb()
			{
				ItemDbId = item.ItemDbId,
				Equipped = item.Equipped
			};

			// You
			Instance.Push(() =>
			{
				using (AppDbContext db = new AppDbContext())
				{
					db.Entry(itemDb).State = EntityState.Unchanged;
					db.Entry(itemDb).Property(nameof(ItemDb.Equipped)).IsModified = true;

					bool success = db.SaveChangesEx();
					if (!success)
					{
						// Fail: Kick
					}
				}
			});
		}
	}
{% endhighlight %}

* Server\Game\Item\Inventory.cs
{% highlight C# %}
public class Inventory
{
  public Dictionary<int, Item> Items { get; } = new Dictionary<int, Item>();

  public void Add(Item item)
  {
    Items.Add(item.ItemDbId, item);
  }

  public Item Get(int itemDbId)
  {
    Item item = null;
    Items.TryGetValue(itemDbId, out item);
    return item;
  }

  public Item Find(Func<Item, bool> condition)
  {
    foreach (Item item in Items.Values)
    {
      if (condition.Invoke(item))
        return item;
    }

    return null;
  }

  public int? GetEmptySlot()
  {
    for (int slot = 0; slot < 20; slot++)
    {
      Item item = Items.Values.FirstOrDefault(i => i.Slot == slot);
      if (item == null)
        return slot;
    }

    return null;
  }
}
{% endhighlight %}

* Server\Game\Item\Item.cs
{% highlight C# %}
public class Item
{
  ...
  public bool Equipped
  {
    get { return Info.Equipped; }
    set { Info.Equipped = value; }
  }
  ...

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

    if (item != null)
    {
      item.ItemDbId = itemDb.ItemDbId;
      item.Count = itemDb.Count;
      item.Slot = itemDb.Slot;
      item.Equipped = itemDb.Equipped;
    }

    return item;
  }
  ...

  public class Consumable : Item
	{
		public ConsumableType ConsumableType { get; private set; }
		public int MaxCount { get; set; }

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
				MaxCount = data.maxCount;
				ConsumableType = data.consumableType;
				Stackable = (data.maxCount > 1);
			}
		}
}
{% endhighlight %}

* Server\Game\Object\Arrow.cs
{% highlight C# %}
public class Arrow : Projectile
{
  public GameObject Owner { get; set; }

  public override void Update()
  {
    if (Data == null || Data.projectile == null || Owner == null || Room == null)
      return;

    int tick = (int)(1000 / Data.projectile.speed);
    Room.PushAfter(tick, Update);

    Vector2Int destPos = GetFrontCellPos();
    if (Room.Map.CanGo(destPos))
    {
      CellPos = destPos;

      S_Move movePacket = new S_Move();
      movePacket.ObjectId = Id;
      movePacket.PosInfo = PosInfo;
      Room.Broadcast(movePacket);

      Console.WriteLine("Move Arrow");
    }
    else
    {
      GameObject target = Room.Map.Find(destPos);
      if (target != null)
      {
        target.OnDamaged(this, Data.damage + Owner.TotalAttack);
      }

      // 소멸
      Room.Push(Room.LeaveGame, Id);
    }
  }

  public override GameObject GetOwner()
  {
    return Owner;
  }
}
{% endhighlight %}

* Server\Game\Object\GameObject.cs
{% highlight C# %}
public class GameObject
{
  ...
  public virtual int TotalAttack { get { return Stat.Attack; } }
  public virtual int TotalDefence { get { return 0; } }
  ...

  public virtual void OnDamaged(GameObject attacker, int damage)
  {
    if (Room == null)
      return;

    damage = Math.Max(damage - TotalDefence, 0);
    Stat.Hp = Math.Max(Stat.Hp - damage, 0);

    S_ChangeHp changePacket = new S_ChangeHp();
    changePacket.ObjectId = Id;
    changePacket.Hp = Stat.Hp;
    Room.Broadcast(changePacket);

    if (Stat.Hp <= 0)
    {
      OnDead(attacker);
    }
  }
  ...
}
{% endhighlight %}

* Server\Game\Object\Monster.cs
{% highlight C# %}
public class Monster : GameObject
{
  ...
  protected virtual void UpdateSkill()
		{
			if (_coolTick == 0)
			{
				if (_target == null || _target.Room != Room)
				{
					_target = null;
					State = CreatureState.Moving;
					BroadcastMove();
					return;
				}

				Vector2Int dir = (_target.CellPos - CellPos);
				int dist = dir.cellDistFromZero;
				bool canUseSkill = (dist <= _skillRange && (dir.x == 0 || dir.y == 0));
				if (canUseSkill == false)
				{
					State = CreatureState.Moving;
					BroadcastMove();
					return;
				}

				MoveDir lookDir = GetDirFromVec(dir);
				if (Dir != lookDir)
				{
					Dir = lookDir;
					BroadcastMove();
				}

				Skill skillData = null;
				DataManager.SkillDict.TryGetValue(1, out skillData);

				_target.OnDamaged(this, skillData.damage + TotalAttack);

				S_Skill skill = new S_Skill() { Info = new SkillInfo() };
				skill.ObjectId = Id;
				skill.Info.SkillId = skillData.id;
				Room.Broadcast(skill);

				int coolTick = (int)(1000 * skillData.cooldown);
				_coolTick = Environment.TickCount64 + coolTick;
			}

			if (_coolTick > Environment.TickCount64)
				return;

			_coolTick = 0;
		}
    ...

		RewardData GetRandomReward()
		{
			MonsterData monsterData = null;
			DataManager.MonsterDict.TryGetValue(TemplateId, out monsterData);

			int rand = new Random().Next(0, 101);

			int sum = 0;
			foreach (RewardData rewardData in monsterData.rewards)
			{
				sum += rewardData.probability;

				if (rand <= sum)
				{
					return rewardData;
				}
			}

			return null;
		}
	}
}
{% endhighlight %}

* Server\Game\Object\Player.cs
{% highlight C# %}
public class Player : GameObject
{
  ...
  public int WeaponDamage { get; private set; }
  public int ArmorDefence { get; private set; }

  public override int TotalAttack { get { return Stat.Attack + WeaponDamage; } }
  public override int TotalDefence { get { return ArmorDefence; } }
  ...

  public void HandleEquipItem(C_EquipItem equipPacket)
  {
    Item item = Inven.Get(equipPacket.ItemDbId);
    if (item == null)
      return;

    if (item.ItemType == ItemType.Consumable)
      return;

    // If setting request, unset duplicated part
    if (equipPacket.Equipped)
    {
      Item unequipItem = null;

      if (item.ItemType == ItemType.Weapon)
      {
        unequipItem = Inven.Find(
          i => i.Equipped && i.ItemType == ItemType.Weapon);
      }
      else if (item.ItemType == ItemType.Armor)
      {
        ArmorType armorType = ((Armor)item).ArmorType;
        unequipItem = Inven.Find(
          i => i.Equipped && i.ItemType == ItemType.Armor
            && ((Armor)i).ArmorType == armorType);
      }

      if (unequipItem != null)
      {
        // first, apply in memory
        unequipItem.Equipped = false;

        // Noti in DB
        DbTransaction.EquipItemNoti(this, unequipItem);

        // Noti in Client
        S_EquipItem equipOkItem = new S_EquipItem();
        equipOkItem.ItemDbId = unequipItem.ItemDbId;
        equipOkItem.Equipped = unequipItem.Equipped;
        Session.Send(equipOkItem);
      }
    }

    {
      // first, apply in memory
      item.Equipped = equipPacket.Equipped;

      // Noti in DB
      DbTransaction.EquipItemNoti(this, item);

      // Noti in Client
      S_EquipItem equipOkItem = new S_EquipItem();
      equipOkItem.ItemDbId = equipPacket.ItemDbId;
      equipOkItem.Equipped = equipPacket.Equipped;
      Session.Send(equipOkItem);
    }

    RefreshAdditionalStat();
  }

  public void RefreshAdditionalStat()
  {
    WeaponDamage = 0;
    ArmorDefence = 0;

    foreach (Item item in Inven.Items.Values)
    {
      if (item.Equipped == false)
        continue;

      switch (item.ItemType)
      {
        case ItemType.Weapon:
          WeaponDamage += ((Weapon)item).Damage;
          break;
        case ItemType.Armor:
          ArmorDefence += ((Armor)item).Defence;
          break;
      }
    }
  }
{% endhighlight %}

* Server\Game\Room\GameRoom.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  ...
  public void Update()
  {
    foreach (Monster monster in _monsters.Values)
    {
      monster.Update();
    }

    Flush();
  }

  public void EnterGame(GameObject gameObject)
  {
    if (gameObject == null)
      return;

    GameObjectType type = ObjectManager.GetObjectTypeById(gameObject.Id);

    if (type == GameObjectType.Player)
    {
      Player player = gameObject as Player;
      _players.Add(gameObject.Id, player);
      player.Room = this;

      player.RefreshAdditionalStat();

      Map.ApplyMove(player, new Vector2Int(player.CellPos.x, player.CellPos.y));

      {
        S_EnterGame enterPacket = new S_EnterGame();
        enterPacket.Player = player.Info;
        player.Session.Send(enterPacket);

        S_Spawn spawnPacket = new S_Spawn();
        foreach (Player p in _players.Values)
        {
          if (player != p)
            spawnPacket.Objects.Add(p.Info);
        }

        foreach (Monster m in _monsters.Values)
          spawnPacket.Objects.Add(m.Info);

        foreach (Projectile p in _projectiles.Values)
          spawnPacket.Objects.Add(p.Info);

        player.Session.Send(spawnPacket);
      }
    }
    else if (type == GameObjectType.Monster)
    {
      Monster monster = gameObject as Monster;
      _monsters.Add(gameObject.Id, monster);
      monster.Room = this;

      Map.ApplyMove(monster, new Vector2Int(monster.CellPos.x, monster.CellPos.y));
    }
    else if (type == GameObjectType.Projectile)
    {
      Projectile projectile = gameObject as Projectile;
      _projectiles.Add(gameObject.Id, projectile);
      projectile.Room = this;

      projectile.Update();
    }
    
    {
      S_Spawn spawnPacket = new S_Spawn();
      spawnPacket.Objects.Add(gameObject.Info);
      foreach (Player p in _players.Values)
      {
        if (p.Id != gameObject.Id)
          p.Session.Send(spawnPacket);
      }
    }
  }

  public void LeaveGame(int objectId)
  {
    GameObjectType type = ObjectManager.GetObjectTypeById(objectId);

    if (type == GameObjectType.Player)
    {
      Player player = null;
      if (_players.Remove(objectId, out player) == false)
        return;

      player.OnLeaveGame();
      Map.ApplyLeave(player);
      player.Room = null;

      {
        S_LeaveGame leavePacket = new S_LeaveGame();
        player.Session.Send(leavePacket);
      }
    }
    else if (type == GameObjectType.Monster)
    {
      Monster monster = null;
      if (_monsters.Remove(objectId, out monster) == false)
        return;

      Map.ApplyLeave(monster);
      monster.Room = null;
    }
    else if (type == GameObjectType.Projectile)
    {
      Projectile projectile = null;
      if (_projectiles.Remove(objectId, out projectile) == false)
        return;

      projectile.Room = null;
    }

    {
      S_Despawn despawnPacket = new S_Despawn();
      despawnPacket.ObjectIds.Add(objectId);
      foreach (Player p in _players.Values)
      {
        if (p.Id != objectId)
          p.Session.Send(despawnPacket);
      }
    }
  }
  ...
}
{% endhighlight %}

* Server\Game\Object\GameRoom_Battle.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  public void HandleMove(Player player, C_Move movePacket)
  {
    if (player == null)
      return;

    PositionInfo movePosInfo = movePacket.PosInfo;
    ObjectInfo info = player.Info;

    if (movePosInfo.PosX != info.PosInfo.PosX || movePosInfo.PosY != info.PosInfo.PosY)
    {
      if (Map.CanGo(new Vector2Int(movePosInfo.PosX, movePosInfo.PosY)) == false)
        return;
    }

    info.PosInfo.State = movePosInfo.State;
    info.PosInfo.MoveDir = movePosInfo.MoveDir;
    Map.ApplyMove(player, new Vector2Int(movePosInfo.PosX, movePosInfo.PosY));

    S_Move resMovePacket = new S_Move();
    resMovePacket.ObjectId = player.Info.ObjectId;
    resMovePacket.PosInfo = movePacket.PosInfo;

    Broadcast(resMovePacket);
  }

  public void HandleSkill(Player player, C_Skill skillPacket)
  {
    if (player == null)
      return;

    ObjectInfo info = player.Info;
    if (info.PosInfo.State != CreatureState.Idle)
      return;

    info.PosInfo.State = CreatureState.Skill;
    S_Skill skill = new S_Skill() { Info = new SkillInfo() };
    skill.ObjectId = info.ObjectId;
    skill.Info.SkillId = skillPacket.Info.SkillId;
    Broadcast(skill);

    Data.Skill skillData = null;
    if (DataManager.SkillDict.TryGetValue(skillPacket.Info.SkillId, out skillData) == false)
      return;

    switch (skillData.skillType)
    {
      case SkillType.SkillAuto:
        {
          Vector2Int skillPos = player.GetFrontCellPos(info.PosInfo.MoveDir);
          GameObject target = Map.Find(skillPos);
          if (target != null)
          {
            Console.WriteLine("Hit GameObject !");
          }
        }
        break;
      case SkillType.SkillProjectile:
        {
          Arrow arrow = ObjectManager.Instance.Add<Arrow>();
          if (arrow == null)
            return;

          arrow.Owner = player;
          arrow.Data = skillData;
          arrow.PosInfo.State = CreatureState.Moving;
          arrow.PosInfo.MoveDir = player.PosInfo.MoveDir;
          arrow.PosInfo.PosX = player.PosInfo.PosX;
          arrow.PosInfo.PosY = player.PosInfo.PosY;
          arrow.Speed = skillData.projectile.speed;
          Push(EnterGame, arrow);
        }
        break;
    }
  }
}
{% endhighlight %}

* Server\Game\Object\GameRoom_Item.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{		
  public void HandleEquipItem(Player player, C_EquipItem equipPacket)
  {
    if (player == null)
      return;

    player.HandleEquipItem(equipPacket);
  }
}
{% endhighlight %}

* Server\Packet\PacketHandler.cs
{% highlight C# %}
public class PAcketHandler
{
  ...
  public static void C_EquipItemHandler(PacketSession session, IMessage packet)
	{
		C_EquipItem equipPacket = (C_EquipItem)packet;
		ClientSession clientSession = (ClientSession)session;

		Player player = clientSession.MyPlayer;
		if (player == null)
			return;

		GameRoom room = player.Room;
		if (room == null)
			return;

		room.Push(room.HandleEquipItem, player, equipPacket);
	}
}
{% endhighlight %}

### DB

<figure>
  <a href="/assets/img/posts/unity_mmocontents/20.jpg"><img src="/assets/img/posts/unity_mmocontents/20.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Item-Use.mp4" frameborder="0"> </iframe>

## Stat UI

* Stat UI Prefab

<figure>
  <a href="/assets/img/posts/unity_mmocontents/21.jpg"><img src="/assets/img/posts/unity_mmocontents/21.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Assets\Scripts\UI\Scene\UI_Stat.cs
{% highlight C# %}
public class UI_Stat : UI_Base
{
    enum Images
    {
        Slot_Helmet,
        Slot_Armor,
        Slot_Boots,
        Slot_Weapon,
        Slot_Shield,
    }

    enum Texts
    {
        NameText,
        AttackValueText,
        DefenceValueText,
    }

    bool _init = false;

    public override void Init()
    {
        Bind<Image>(typeof(Images));
        Bind<Text>(typeof(Texts));

        _init = true;
        RefreshUI();
    }

    public void RefreshUI()
    {
        if (_init == false)
            return;

        // Hide all at first
        Get<Image>((int)Images.Slot_Helmet).enabled = false;
        Get<Image>((int)Images.Slot_Armor).enabled = false;
        Get<Image>((int)Images.Slot_Boots).enabled = false;
        Get<Image>((int)Images.Slot_Weapon).enabled = false;
        Get<Image>((int)Images.Slot_Shield).enabled = false;

        // Fill
        foreach (Item item in Managers.Inven.Items.Values)
        {
            if (item.Equipped == false)
                continue;

            ItemData itemData = null;
            Managers.Data.ItemDict.TryGetValue(item.TemplateId, out itemData);

            Sprite icon = Managers.Resource.Load<Sprite>(itemData.iconPath);

            if(item.ItemType == ItemType.Weapon)
            {
                Get<Image>((int)Images.Slot_Weapon).enabled = true;
                Get<Image>((int)Images.Slot_Weapon).sprite = icon;
            }
            else if(item.ItemType == ItemType.Armor)
            {
                Armor armor = (Armor)item;
                switch (armor.ArmorType)
                {
                    case ArmorType.Helmet:
                        Get<Image>((int)Images.Slot_Helmet).enabled = true;
                        Get<Image>((int)Images.Slot_Helmet).sprite = icon;
                        break;
                    case ArmorType.Armor:
                        Get<Image>((int)Images.Slot_Armor).enabled = true;
                        Get<Image>((int)Images.Slot_Armor).sprite = icon;
                        break;
                    case ArmorType.Boots:
                        Get<Image>((int)Images.Slot_Boots).enabled = true;
                        Get<Image>((int)Images.Slot_Boots).sprite = icon;
                        break;
                }
            }
        }

        // Text
        MyPlayerController player = Managers.Object.MyPlayer;
        player.RefreshAdditionalStat();

        Get<Text>((int)Texts.NameText).text = player.name;
        int totalDamage = player.Stat.Attack + player.WeaponDamage;
        Get<Text>((int)Texts.AttackValueText).text = $"{totalDamage}(+{player.WeaponDamage})";
        Get<Text>((int)Texts.DefenceValueText).text = $"{player.ArmorDefence}";
    }
}
{% endhighlight %}

* Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
  ...
  public static void S_AddItemHandler(PacketSession session, IMessage packet)
	{
		S_AddItem itemList = (S_AddItem)packet;

		foreach (ItemInfo itemInfo in itemList.Items)
		{
			Item item = Item.MakeItem(itemInfo);
			Managers.Inven.Add(item);
		}

		Debug.Log("Get Item!");

		UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
		gameSceneUI.InvenUI.RefreshUI();
		gameSceneUI.StatUI.RefreshUI();

		if (Managers.Object.MyPlayer != null)
			Managers.Object.MyPlayer.RefreshAdditionalStat();
	}

	public static void S_EquipItemHandler(PacketSession session, IMessage packet)
	{
		S_EquipItem equipItemOk = (S_EquipItem)packet;

		Item item = Managers.Inven.Get(equipItemOk.ItemDbId);
		if (item == null)
			return;

		item.Equipped = equipItemOk.Equipped;
		Debug.Log("Change setted item!");

		UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
		gameSceneUI.InvenUI.RefreshUI();
		gameSceneUI.StatUI.RefreshUI();

		if (Managers.Object.MyPlayer != null)
			Managers.Object.MyPlayer.RefreshAdditionalStat();
	}
  ...
}
{% endhighlight %}

* Assets\Scripts\Controllers\BaseController.cs
{% highlight C# %}
public class BaseController : MonoBehaviour
{
	public int Id { get; set; }

	StatInfo _stat = new StatInfo();
	public virtual StatInfo Stat
	{
		get { return _stat; }
		set
		{
			if (_stat.Equals(value))
				return;

			_stat.MergeFrom(value);
		}
	}
  ...
}
{% endhighlight %}

* Assets\Scripts\Controllers\MyPlayerController.cs
{% highlight C# %}
public class MyPlayerController : PlayerController
{
  ...
  void GetUIKeyInput()
	{
		if (Input.GetKeyDown(KeyCode.I))
		{
			UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
			UI_Inventory invenUI = gameSceneUI.InvenUI;

			if (invenUI.gameObject.activeSelf)
			{
				invenUI.gameObject.SetActive(false);
			}
			else
			{
				invenUI.gameObject.SetActive(true);
				invenUI.RefreshUI();
			}
		}
		else if (Input.GetKeyDown(KeyCode.C))
		{
			UI_GameScene gameSceneUI = Managers.UI.SceneUI as UI_GameScene;
			UI_Stat statUI = gameSceneUI.StatUI;

			if (statUI.gameObject.activeSelf)
			{
				statUI.gameObject.SetActive(false);
			}
			else
			{
				statUI.gameObject.SetActive(true);
				statUI.RefreshUI();
			}
		}
	}
  ...
}
{% endhighlight %}

* Assets\Scripts\Managers\Core\DataManager.cs
{% highlight C# %}
...
public class DataManager
{
  ...
  Loader LoadJson<Loader, Key, Value>(string path) where Loader : ILoader<Key, Value>
    {
		TextAsset textAsset = Managers.Resource.Load<TextAsset>($"Data/{path}");
        return Newtonsoft.Json.JsonConvert.DeserializeObject<Loader>(textAsset.text);
	}
}
{% endhighlight %}

* Assets\Scripts\Managers\Contents\ObjectManager.cs
{% highlight C# %}
public class ObjectManager
{
	...
	public void Add(ObjectInfo info, bool myPlayer = false)
	{
		GameObjectType objectType = GetObjectTypeById(info.ObjectId);
		if (objectType == GameObjectType.Player)
		{
			if (myPlayer)
			{
				GameObject go = Managers.Resource.Instantiate("Creature/MyPlayer");
				go.name = info.Name;
				_objects.Add(info.ObjectId, go);

				MyPlayer = go.GetComponent<MyPlayerController>();
				MyPlayer.Id = info.ObjectId;
				MyPlayer.PosInfo = info.PosInfo;
				MyPlayer.Stat.MergeFrom(info.StatInfo);
				MyPlayer.SyncPos();
			}
			else
			{
				GameObject go = Managers.Resource.Instantiate("Creature/Player");
				go.name = info.Name;
				_objects.Add(info.ObjectId, go);

				PlayerController pc = go.GetComponent<PlayerController>();
				pc.Id = info.ObjectId;
				pc.PosInfo = info.PosInfo;
				pc.Stat.MergeFrom(info.StatInfo);
				pc.SyncPos();
			}
		}
		else if (objectType == GameObjectType.Monster)
		{
			GameObject go = Managers.Resource.Instantiate("Creature/Monster");
			go.name = info.Name;
			_objects.Add(info.ObjectId, go);

			MonsterController mc = go.GetComponent<MonsterController>();
			mc.Id = info.ObjectId;
			mc.PosInfo = info.PosInfo;
			mc.Stat = info.StatInfo;
			mc.SyncPos();
		}
		else if (objectType == GameObjectType.Projectile)
		{
			GameObject go = Managers.Resource.Instantiate("Creature/Arrow");
			go.name = "Arrow";
			_objects.Add(info.ObjectId, go);

			ArrowController ac = go.GetComponent<ArrowController>();
			ac.PosInfo = info.PosInfo;
			ac.Stat = info.StatInfo;
			ac.SyncPos();
		}
	}
  ...
}
{% endhighlight %}

* Assets\Sciprts\UI\Scene\UI_Inventory_Item.cs
{% highlight C# %}
public class UI_Inventory_Item : UI_Base
{
	...
	public override void Init()
	{
		_icon.gameObject.BindEvent((e) =>
		{
			Debug.Log("Click Item");

			Data.ItemData itemData = null;
			Managers.Data.ItemDict.TryGetValue(TemplateId, out itemData);

			if (itemData == null)
				return;

			if (itemData.itemType == ItemType.Consumable)
				return;

			C_EquipItem equipPacket = new C_EquipItem();
			equipPacket.ItemDbId = ItemDbId;
			equipPacket.Equipped = !Equipped;

			Managers.Network.Send(equipPacket);
		});
	}

	public void SetItem(Item item)
	{
		if(item == null)
        {
			ItemDbId = 0;
			TemplateId = 0;
			Count = 0;
			Equipped = false;

			_icon.gameObject.SetActive(false);
			_frame.gameObject.SetActive(false);
        }
        else
        {
			ItemDbId = item.ItemDbId;
			TemplateId = item.TemplateId;
			Count = item.Count;
			Equipped = item.Equipped;

			Data.ItemData itemData = null;
			Managers.Data.ItemDict.TryGetValue(TemplateId, out itemData);

			Sprite icon = Managers.Resource.Load<Sprite>(itemData.iconPath);
			_icon.sprite = icon;

			_icon.gameObject.SetActive(true);
			_frame.gameObject.SetActive(Equipped);
		}
	}
}
{% endhighlight %}

* Add Library

<figure>
  <a href="/assets/img/posts/unity_mmocontents/22.jpg"><img src="/assets/img/posts/unity_mmocontents/22.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Stat-UI.mp4" frameborder="0"> </iframe>

### Large Structure Management

* Server\Job\Job.cs
{% highlight C# %}
public abstract class IJob
{
  public abstract void Execute();
  public bool Cancel { get; set; } = false;
}

public class Job : IJob
{
  Action _action;

  public Job(Action action)
  {
    _action = action;
  }

  public override void Execute()
  {
    if(Cancel == false)
      _action.Invoke();
  }
}

public class Job<T1> : IJob
{
  Action<T1> _action;
  T1 _t1;

  public Job(Action<T1> action, T1 t1)
  {
    _action = action;
    _t1 = t1;
  }

  public override void Execute()
  {
    if (Cancel == false)
      _action.Invoke(_t1);
  }
}

public class Job<T1, T2> : IJob
{
  Action<T1, T2> _action;
  T1 _t1;
  T2 _t2;

  public Job(Action<T1, T2> action, T1 t1, T2 t2)
  {
    _action = action;
    _t1 = t1;
    _t2 = t2;
  }

  public override void Execute()
  {
    if (Cancel == false)
      _action.Invoke(_t1, _t2);
  }
}

public class Job<T1, T2, T3> : IJob
{
  Action<T1, T2, T3> _action;
  T1 _t1;
  T2 _t2;
  T3 _t3;

  public Job(Action<T1, T2, T3> action, T1 t1, T2 t2, T3 t3)
  {
    _action = action;
    _t1 = t1;
    _t2 = t2;
    _t3 = t3;
  }

  public override void Execute()
  {
    if (Cancel == false)
      _action.Invoke(_t1, _t2, _t3);
  }
}
{% endhighlight %}

* Server\Job\JobSerializer.cs
{% highlight C# %}
public class JobSerializer
{
  JobTimer _timer = new JobTimer();
  Queue<IJob> _jobQueue = new Queue<IJob>();
  object _lock = new object();
  bool _flush = false;

  public IJob PushAfter(int tickAfter, Action action) { return PushAfter(tickAfter, new Job(action)); }
  public IJob PushAfter<T1>(int tickAfter, Action<T1> action, T1 t1) { return PushAfter(tickAfter, new Job<T1>(action, t1)); }
  public IJob PushAfter<T1, T2>(int tickAfter, Action<T1, T2> action, T1 t1, T2 t2) { return PushAfter(tickAfter, new Job<T1, T2>(action, t1, t2)); }
  public IJob PushAfter<T1, T2, T3>(int tickAfter, Action<T1, T2, T3> action, T1 t1, T2 t2, T3 t3) { return PushAfter(tickAfter, new Job<T1, T2, T3>(action, t1, t2, t3)); }

  public IJob PushAfter(int tickAfter, IJob job)
  {
    _timer.Push(job, tickAfter);
    return job;
  }

  public void Push(Action action) { Push(new Job(action)); }
  public void Push<T1>(Action<T1> action, T1 t1) { Push(new Job<T1>(action, t1)); }
  public void Push<T1, T2>(Action<T1, T2> action, T1 t1, T2 t2) { Push(new Job<T1, T2>(action, t1, t2)); }
  public void Push<T1, T2, T3>(Action<T1, T2, T3> action, T1 t1, T2 t2, T3 t3) { Push(new Job<T1, T2, T3>(action, t1, t2, t3)); }

  public void Push(IJob job)
  {
    lock (_lock)
    {
      _jobQueue.Enqueue(job);
    }
  }

  public void Flush()
  {
    _timer.Flush();

    while (true)
    {
      IJob job = Pop();
      if (job == null)
        return;

      job.Execute();
    }
  }

  IJob Pop()
  {
    lock (_lock)
    {
      if (_jobQueue.Count == 0)
      {
        _flush = false;
        return null;
      }
      return _jobQueue.Dequeue();
    }
  }
}
{% endhighlight %}

* Server\Object\Monster.cs
{% highlight C# %}
public class Monster : GameObject
{
  ...
  IJob _job;
  public override void Update()
  {
    switch (State)
    {
      case CreatureState.Idle:
        UpdateIdle();
        break;
      case CreatureState.Moving:
        UpdateMoving();
        break;
      case CreatureState.Skill:
        UpdateSkill();
        break;
      case CreatureState.Dead:
        UpdateDead();
        break;
    }

    // 5프레임 (0.2초마다 한번씩 Update)
    if (Room != null)
      _job = Room.PushAfter(200, Update);
  }
  
  ...
  public override void OnDead(GameObject attacker)
  {
    if (_job != null)
    {
      _job.Cancel = true;
      _job = null;
    }

    base.OnDead(attacker);

    GameObject owner = attacker.GetOwner();
    if (owner.ObjectType == GameObjectType.Player)
    {
      RewardData rewardData = GetRandomReward();
      if (rewardData != null)
      {
        Player player = (Player)owner;
        DbTransaction.RewardPlayer(player, rewardData, Room);
      }
    }
  }
  ...
}
{% endhighlight %}

* Server\Room\GameRoom.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  ...
  public void Update()
  {
    Flush();
  }
  ...

  public void EnterGame(GameObject gameObject)
  {
    ...
    else if (type == GameObjectType.Monster)
    {
      Monster monster = gameObject as Monster;
      _monsters.Add(gameObject.Id, monster);
      monster.Room = this;

      Map.ApplyMove(monster, new Vector2Int(monster.CellPos.x, monster.CellPos.y));

      monster.Update();
    }
    ...
  }
  ...
}
{% endhighlight %}

## Game Structure change

* Server\Game\Room\GameLogic.cs
  - Change RoomManager.cs to GameLogic.cs

{% highlight C# %}
public class GameLogic : JobSerializer
{
    public static GameLogic Instance { get; } = new GameLogic();

    Dictionary<int, GameRoom> _rooms = new Dictionary<int, GameRoom>();
    int _roomId = 1;

    public void Update()
    {
        Flush();

        foreach (GameRoom room in _rooms.Values)
        {
            room.Update();
        }
    }

    public GameRoom Add(int mapId)
    {
        GameRoom gameRoom = new GameRoom();
        gameRoom.Push(gameRoom.Init, mapId);

        gameRoom.RoomId = _roomId;
        _rooms.Add(_roomId, gameRoom);
        _roomId++;

        return gameRoom;
    }

    public bool Remove(int roomId)
    {

        return _rooms.Remove(roomId);
    }

    public GameRoom Find(int roomId)
    {

        GameRoom room = null;
        if (_rooms.TryGetValue(roomId, out room))
            return room;

        return null;

    }
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  public PlayerServerState ServerState { get; private set; } = PlayerServerState.ServerStateLogin;

  public Player MyPlayer { get; set; }
  public int SessionId { get; set; }

  object _lock = new object();
  List<ArraySegment<byte>> _reserveQueue = new List<ArraySegment<byte>>();

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

    lock (_lock)
    {
      _reserveQueue.Add(sendBuffer);
    }
  }

  public void FlushSend()
  {
    List<ArraySegment<byte>> sendList = null;

    lock (_lock)
    {
      if (_reserveQueue.Count == 0)
      return;

      sendList = _reserveQueue;
      _reserveQueue = new List<ArraySegment<byte>>();
    }

    Send(sendList);
  }

  ...

  public override void OnDisconnected(EndPoint endPoint)
  {
    GameLogic.Instance.Push(() =>
    {
      GameRoom room = GameLogic.Instance.Find(1);
      room.Push(room.LeaveGame, MyPlayer.Info.ObjectId);
    });
    
    SessionManager.Instance.Remove(this);

    Console.WriteLine($"OnDisconnected : {endPoint}");
  }

  public override void OnSend(int numOfBytes){ }
  #endregion
}
{% endhighlight %}

* Server\Session\ClientSession_PreGame.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  ...

  public void HandleEnterGame(C_EnterGame enterGamePacket)
  {
    ...

    GameLogic.Instance.Push(() =>
    {
      GameRoom room = GameLogic.Instance.Find(1);
      room.Push(room.EnterGame, MyPlayer);
    });
  }

  ...
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

  public List<ClientSession> GetSessions()
  {
    List<ClientSession> sessions = new List<ClientSession>();

    lock (_lock)
    {
      sessions = _sessions.Values.ToList();
    }

    return sessions;
  }
  ...
}
{% endhighlight %}

* Server\Program.cs
{% highlight C# %}
class Program
{
  static Listener _listener = new Listener();

  static void GameLogicTask()
  {
    while (true)
    {
      GameLogic.Instance.Update();
      Thread.Sleep(0);
    }
  }

  static void DbTask()
  {
    while (true)
    {
      DbTransaction.Instance.Flush();
      Thread.Sleep(0);
    }
  }

  static void NetworkTask()
  {
    while (true)
    {
      List<ClientSession> sessions = SessionManager.Instance.GetSessions();
      foreach(ClientSession session in sessions)
      {
        session.FlushSend();
      }

      Thread.Sleep(0);
    }
  }

  static void Main(string[] args)
  {
    ConfigManager.LoadConfig();
    DataManager.LoadData();

    GameLogic.Instance.Push(() => {	GameLogic.Instance.Add(1); });
    

    // DNS (Domain Name System)
    string host = Dns.GetHostName();
    IPHostEntry ipHost = Dns.GetHostEntry(host);
    IPAddress ipAddr = ipHost.AddressList[0];
    IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

    _listener.Init(endPoint, () => { return SessionManager.Instance.Generate(); });
    Console.WriteLine("Listening...");

    // GameLogicTask
    {
      Task gameLogicTask = new Task(GameLogicTask, TaskCreationOptions.LongRunning);
      gameLogicTask.Start();
    }

    // NetworkTask
    {
      Task networkTask = new Task(NetworkTask, TaskCreationOptions.LongRunning);
      networkTask.Start();
    }

    // DbTask
    {
      DbTask();
    }
  }
}
{% endhighlight %}

## Zone

* Zone
- Current game manages whole section of game
- From now on, game should be managed by spilited section
- This is good for performance because game doesn't need to manage whole game

* Client\Assets\Scripts\Managers\Contents\ObjectManager.cs
{% highlight C# %}
public class ObjectManager
{
  ...
  public void Add(ObjectInfo info, bool myPlayer = false)
	{
		if (MyPlayer != null && MyPlayer.Id == info.ObjectId)
			return;
		if (_objects.ContainsKey(info.ObjectId))
			return;
    ...
  }
  ...

  public void Remove(int id)
	{
		if (MyPlayer != null && MyPlayer.Id == id)
			return;
		if (_objects.ContainsKey(id) == false)
			return;
    ...
  }
  ...
}
{% endhighlight %}

* Server\Game\Object\Arrow.cs
{% highlight C# %}
public class Arrow : Projectile
{
  public GameObject Owner { get; set; }

  public override void Update()
  {
    if (Data == null || Data.projectile == null || Owner == null || Room == null)
      return;

    int tick = (int)(1000 / Data.projectile.speed);
    Room.PushAfter(tick, Update);

    Vector2Int destPos = GetFrontCellPos();
    if (Room.Map.ApplyMove(this, destPos, collision: false))
    {
      S_Move movePacket = new S_Move();
      movePacket.ObjectId = Id;
      movePacket.PosInfo = PosInfo;
      Room.Broadcast(CellPos, movePacket);

      Console.WriteLine("Move Arrow");
    }
    ...
  }
  ...
}
{% endhighlight %}

* Server\Game\Object\GameObject.cs
{% highlight C# %}
public class GameObject
{
  ...
  public virtual void OnDamaged(GameObject attacker, int damage)
  {
    if (Room == null)
      return;

    damage = Math.Max(damage - TotalDefence, 0);
    Stat.Hp = Math.Max(Stat.Hp - damage, 0);

    S_ChangeHp changePacket = new S_ChangeHp();
    changePacket.ObjectId = Id;
    changePacket.Hp = Stat.Hp;
    Room.Broadcast(CellPos, changePacket);

    if (Stat.Hp <= 0)
    {
      OnDead(attacker);
    }
  }

  public virtual void OnDead(GameObject attacker)
  {
    if (Room == null)
      return;

    S_Die diePacket = new S_Die();
    diePacket.ObjectId = Id;
    diePacket.AttackerId = attacker.Id;
    Room.Broadcast(CellPos, diePacket);

    GameRoom room = Room;
    room.LeaveGame(Id);

    Stat.Hp = Stat.MaxHp;
    PosInfo.State = CreatureState.Idle;
    PosInfo.MoveDir = MoveDir.Down;

    room.EnterGame(this, randomPos: true);
  }
  ...
}
{% endhighlight %}


* Server\Game\Object\Monster.cs
{% highlight C# %}
public class Monster : GameObject
{
  void BroadcastMove()
		{
			S_Move movePacket = new S_Move();
			movePacket.ObjectId = Id;
			movePacket.PosInfo = PosInfo;
			Room.Broadcast(CellPos, movePacket);
		}

		long _coolTick = 0;
		protected virtual void UpdateSkill()
		{
			if (_coolTick == 0)
			{
				if (_target == null || _target.Room != Room)
				{
					_target = null;
					State = CreatureState.Moving;
					BroadcastMove();
					return;
				}

				Vector2Int dir = (_target.CellPos - CellPos);
				int dist = dir.cellDistFromZero;
				bool canUseSkill = (dist <= _skillRange && (dir.x == 0 || dir.y == 0));
				if (canUseSkill == false)
				{
					State = CreatureState.Moving;
					BroadcastMove();
					return;
				}

				MoveDir lookDir = GetDirFromVec(dir);
				if (Dir != lookDir)
				{
					Dir = lookDir;
					BroadcastMove();
				}

				Skill skillData = null;
				DataManager.SkillDict.TryGetValue(1, out skillData);

				_target.OnDamaged(this, skillData.damage + TotalAttack);

				S_Skill skill = new S_Skill() { Info = new SkillInfo() };
				skill.ObjectId = Id;
				skill.Info.SkillId = skillData.id;
				Room.Broadcast(CellPos, skill);

				int coolTick = (int)(1000 * skillData.cooldown);
				_coolTick = Environment.TickCount64 + coolTick;
			}

			if (_coolTick > Environment.TickCount64)
				return;

			_coolTick = 0;
		}
}
{% endhighlight %}


* Server\Game\Object\Player.cs
{% highlight C# %}
public class Player : GameObject
{
  ...
  public VisionCube Vision { get; private set; }
  ...

  public Player()
  {
    ObjectType = GameObjectType.Player;
    Vision = new VisionCube(this);
  }
  ...
}
{% endhighlight %}

* Server\Game\Room\GameLogic.cs
{% highlight C# %}
public class GameLogic : JobSerializer
{
  ...
  public GameRoom Add(int mapId)
  {
      GameRoom gameRoom = new GameRoom();
      gameRoom.Push(gameRoom.Init, mapId, 10);

      gameRoom.RoomId = _roomId;
      _rooms.Add(_roomId, gameRoom);
      _roomId++;

      return gameRoom;
  }
  ...
}
{% endhighlight %}

* Server\Game\Room\GameRoom.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  public const int VisionCells = 5;

  public int RoomId { get; set; }

  Dictionary<int, Player> _players = new Dictionary<int, Player>();
  Dictionary<int, Monster> _monsters = new Dictionary<int, Monster>();
  Dictionary<int, Projectile> _projectiles = new Dictionary<int, Projectile>();

  public Zone[,] Zones { get; private set; }
  public int ZoneCells { get; private set; }

  public Map Map { get; private set; } = new Map();

  public Zone GetZone(Vector2Int cellPos)
      {
    int x = (cellPos.x - Map.MinX) / ZoneCells;
    int y = (Map.MaxY - cellPos.y) / ZoneCells;

    if (x < 0 || x >= Zones.GetLength(1))
      return null;
    if (y < 0 || y >= Zones.GetLength(0))
      return null;

    return Zones[y, x];
      }

  public void Init(int mapId, int zoneCells)
  {
    Map.LoadMap(mapId);

    // Zone
    ZoneCells = zoneCells;
    int countY = (Map.SizeY + zoneCells - 1) / zoneCells;
    int countX = (Map.SizeX + zoneCells - 1) / zoneCells;
    Zones = new Zone[countY, countX];

    for(int y=0; y<countY; y++)
          {
      for(int x=0; x<countX; x++)
              {
        Zones[y, x] = new Zone(y, x);
              }
          }

    Monster monster = ObjectManager.Instance.Add<Monster>();
    monster.Init(1);
    EnterGame(monster, randomPos: true);
  }

  public void Update()
  {
    Flush();
  }

  Random _rand = new Random();
  public void EnterGame(GameObject gameObject, bool randomPos)
  {
    if (gameObject == null)
      return;

          if (randomPos)
          {
      Vector2Int respawnPos;
      while (true)
      {
        respawnPos.x = _rand.Next(Map.MinX, Map.MaxX + 1);
        respawnPos.y = _rand.Next(Map.MinY, Map.MaxY + 1);
        if (Map.Find(respawnPos) == null)
        {
          gameObject.CellPos = respawnPos;
          break;
        }
      }
    }

    GameObjectType type = ObjectManager.GetObjectTypeById(gameObject.Id);

    if (type == GameObjectType.Player)
    {
      Player player = gameObject as Player;
      _players.Add(gameObject.Id, player);
      player.Room = this;

      player.RefreshAdditionalStat();

      Map.ApplyMove(player, new Vector2Int(player.CellPos.x, player.CellPos.y));
      GetZone(player.CellPos).Players.Add(player);

      {
        S_EnterGame enterPacket = new S_EnterGame();
        enterPacket.Player = player.Info;
        player.Session.Send(enterPacket);

        player.Vision.Update();
      }
    }
    else if (type == GameObjectType.Monster)
    {
      Monster monster = gameObject as Monster;
      _monsters.Add(gameObject.Id, monster);
      monster.Room = this;

      GetZone(monster.CellPos).Monsters.Add(monster);
      Map.ApplyMove(monster, new Vector2Int(monster.CellPos.x, monster.CellPos.y));
      monster.Update();
    }
    else if (type == GameObjectType.Projectile)
    {
      Projectile projectile = gameObject as Projectile;
      _projectiles.Add(gameObject.Id, projectile);
      projectile.Room = this;

      GetZone(projectile.CellPos).Projectiles.Add(projectile);
      projectile.Update();
    }

    {
      S_Spawn spawnPacket = new S_Spawn();
      spawnPacket.Objects.Add(gameObject.Info);
      Broadcast(gameObject.CellPos, spawnPacket);
    }
  }

  public void LeaveGame(int objectId)
  {
    GameObjectType type = ObjectManager.GetObjectTypeById(objectId);
    Vector2Int cellPos;

    if (type == GameObjectType.Player)
    {
      Player player = null;
      if (_players.Remove(objectId, out player) == false)
        return;

      cellPos = player.CellPos;

      player.OnLeaveGame();
      Map.ApplyLeave(player);
      player.Room = null;

      {
        S_LeaveGame leavePacket = new S_LeaveGame();
        player.Session.Send(leavePacket);
      }
    }
    else if (type == GameObjectType.Monster)
    {
      Monster monster = null;
      if (_monsters.Remove(objectId, out monster) == false)
        return;

      cellPos = monster.CellPos;

      Map.ApplyLeave(monster);
      monster.Room = null;
    }
    else if (type == GameObjectType.Projectile)
    {
      Projectile projectile = null;
      if (_projectiles.Remove(objectId, out projectile) == false)
        return;

      cellPos = projectile.CellPos;
      Map.ApplyLeave(projectile);
      projectile.Room = null;
    }
          else
          {
      return;
    }

    {
      S_Despawn despawnPacket = new S_Despawn();
      despawnPacket.ObjectIds.Add(objectId);
      Broadcast(cellPos, despawnPacket);
    }
  }

  public Player FindPlayer(Func<GameObject, bool> condition)
  {
    foreach (Player player in _players.Values)
    {
      if (condition.Invoke(player))
        return player;
    }

    return null;
  }

  public void Broadcast(Vector2Int pos, IMessage packet)
  {
    List<Zone> zones = GetAdjacentZones(pos);

    foreach(Player p in zones.SelectMany(z => z.Players))
    {
      int dx = p.CellPos.x - pos.x;
      int dy = p.CellPos.y - pos.y;
      if (Math.Abs(dx) > GameRoom.VisionCells)
        continue;
      if (Math.Abs(dy) > GameRoom.VisionCells)
        continue;

      p.Session.Send(packet);
    }
  }

  public List<Zone> GetAdjacentZones(Vector2Int cellPos, int cells = GameRoom.VisionCells)
  {
    HashSet<Zone> zones = new HashSet<Zone>();

    int[] delta = new int[2] { -cells, +cells };
    foreach(int dy in delta)
    {
      foreach(int dx in delta)
      {
        int y = cellPos.y + dy;
        int x = cellPos.x + dx;
        Zone zone = GetZone(new Vector2Int(x, y));
        if (zone == null)
          continue;

        zones.Add(zone);
      }
    }

    return zones.ToList();
  }
}
{% endhighlight %}

* Server\Game\Room\GameRoom_Battle.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  public void HandleMove(Player player, C_Move movePacket)
  {
    ...
    Broadcast(player.CellPos, resMovePacket);
  }

  public void HandleSkill(Player player, C_Skill skillPacket)
  {
    ...
    Broadcast(player.CellPos, skill);
    ...

        case SkillType.SkillProjectile:
        {
          ...
          Push(EnterGame, arrow, false);
        }
        break;
  }
}
{% endhighlight %}

* Server\Game\Room\Map.cs
{% highlight C# %}
public struct Pos
{
  ...
  
  public static bool operator==(Pos lhs, Pos rhs)
  {
    return lhs.Y == rhs.Y && lhs.X == rhs.X;
  }

  public static bool operator!=(Pos lhs, Pos rhs)
  {
    return !(lhs == rhs);
  }

  public override bool Equals(object obj)
  {
    return (Pos)obj == this;
  }

  public override int GetHashCode()
  {
    long value = (Y << 32) | X;
    return value.GetHashCode();
  }

  public override string ToString()
  {
    return base.ToString();
  }
}
...

public class Map
{
  ...

  public bool ApplyLeave(GameObject gameObject)
  {
    if (gameObject.Room == null)
      return false;
    if (gameObject.Room.Map != this)
      return false;

    PositionInfo posInfo = gameObject.PosInfo;
    if (posInfo.PosX < MinX || posInfo.PosX > MaxX)
      return false;
    if (posInfo.PosY < MinY || posInfo.PosY > MaxY)
      return false;

    // Zone
    Zone zone = gameObject.Room.GetZone(gameObject.CellPos);
    zone.Remove(gameObject);

    {
      int x = posInfo.PosX - MinX;
      int y = MaxY - posInfo.PosY;
      if (_objects[y, x] == gameObject)
        _objects[y, x] = null;
    }

    return true;
  }

  public bool ApplyMove(GameObject gameObject, Vector2Int dest, bool checkObjects = true, bool collision = true)
  {
    if (gameObject.Room == null)
      return false;
    if (gameObject.Room.Map != this)
      return false;

    PositionInfo posInfo = gameObject.PosInfo;
    if (CanGo(dest, checkObjects) == false)
      return false;

    if(collision)
    {
      // Remove
      {
        int x = posInfo.PosX - MinX;
        int y = MaxY - posInfo.PosY;
        if (_objects[y, x] == gameObject)
          _objects[y, x] = null;
      }
              {
        int x = dest.x - MinX;
        int y = MaxY - dest.y;
        _objects[y, x] = gameObject;
      }
    }

    // Zone
    GameObjectType type = ObjectManager.GetObjectTypeById(gameObject.Id);
    if (type == GameObjectType.Player)
    {
      Player player = (Player)gameObject;
      Zone now = gameObject.Room.GetZone(gameObject.CellPos);
      Zone after = gameObject.Room.GetZone(dest);
      if (now != after)
      {
        now.Players.Remove(player);
        after.Players.Add(player);
      }
    }
    else if (type == GameObjectType.Monster)
          {
      Monster monster = (Monster)gameObject;
      Zone now = gameObject.Room.GetZone(gameObject.CellPos);
      Zone after = gameObject.Room.GetZone(dest);
      if (now != after)
      {
        now.Monsters.Remove(monster);
        after.Monsters.Add(monster);
      }
    }
    else if(type == GameObjectType.Projectile)
          {
      Projectile projectile = (Projectile)gameObject;
      Zone now = gameObject.Room.GetZone(gameObject.CellPos);
      Zone after = gameObject.Room.GetZone(dest);
      if (now != after)
      {
        now.Projectiles.Remove(projectile);
        after.Projectiles.Add(projectile);
      }
    }
    ...
  }

  #region A* PathFinding
  ... 

  // Change Array to Structure
  public List<Vector2Int> FindPath(Vector2Int startCellPos, Vector2Int destCellPos, bool checkObjects = true)
  {
    List<Pos> path = new List<Pos>();
    HashSet<Pos> closeList = new HashSet<Pos>();

    Dictionary<Pos, int> openList = new Dictionary<Pos, int>();
    Dictionary<Pos, Pos> parent = new Dictionary<Pos, Pos>();

    PriorityQueue<PQNode> pq = new PriorityQueue<PQNode>();

    // CellPos -> ArrayPos
    Pos pos = Cell2Pos(startCellPos);
    Pos dest = Cell2Pos(destCellPos);

    openList.Add(pos, 10 * (Math.Abs(dest.Y - pos.Y) + Math.Abs(dest.X - pos.X)));

    pq.Push(new PQNode() { F = 10 * (Math.Abs(dest.Y - pos.Y) + Math.Abs(dest.X - pos.X)), G = 0, Y = pos.Y, X = pos.X });
    parent.Add(pos, pos);

    while (pq.Count > 0)
    {
      PQNode pqNode = pq.Pop();
      Pos node = new Pos(pqNode.Y, pqNode.X);
      if (closeList.Contains(node))
        continue;

      closeList.Add(node);
      if (node.Y == dest.Y && node.X == dest.X)
        break;

      for (int i = 0; i < _deltaY.Length; i++)
      {
        Pos next = new Pos(node.Y + _deltaY[i], node.X + _deltaX[i]);

        if (next.Y != dest.Y || next.X != dest.X)
        {
          if (CanGo(Pos2Cell(next), checkObjects) == false) // CellPos
            continue;
        }

        if (closeList.Contains(next))
          continue;

        int g = 0;// node.G + _cost[i];
        int h = 10 * ((dest.Y - next.Y) * (dest.Y - next.Y) + (dest.X - next.X) * (dest.X - next.X));

        int value = 0;
        if (openList.TryGetValue(next, out value) == false)
          value = Int32.MaxValue;

        if (value < g + h)
          continue;

        if (openList.TryAdd(next, g + h) == false)
          openList[next] = g + h;

        pq.Push(new PQNode() { F = g + h, G = g, Y = next.Y, X = next.X });

        if (parent.TryAdd(next, node) == false)
          parent[next] = node;
      }
    }

    return CalcCellPathFromParent(parent, dest);
  }

  List<Vector2Int> CalcCellPathFromParent(Dictionary<Pos, Pos> parent, Pos dest)
  {
    List<Vector2Int> cells = new List<Vector2Int>();

    Pos pos = dest;

    while (parent[pos] != pos)
    {
      cells.Add(Pos2Cell(pos));
      pos = parent[pos];
    }
    cells.Add(Pos2Cell(pos));
    cells.Reverse();

    return cells;
  }
  ...
  #endregion
}
{% endhighlight %}

* Server\Game\Room\VisionCube.cs
{% highlight C# %}
public class VisionCube
{
    public Player Owner { get; private set; }
    public HashSet<GameObject> PreviousObjects { get; private set; } = new HashSet<GameObject>();

    public VisionCube(Player owner)
    {
        Owner = owner;
    }

    public HashSet<GameObject> GatherObjects()
    {
        if (Owner == null || Owner.Room == null)
            return null;

        HashSet<GameObject> objects = new HashSet<GameObject>();

        Vector2Int cellPos = Owner.CellPos;
        List<Zone> zones = Owner.Room.GetAdjacentZones(cellPos);

        foreach(Zone zone in zones)
        {
            foreach(Player player in zone.Players)
            {
                int dx = player.CellPos.x - cellPos.x;
                int dy = player.CellPos.y - cellPos.y;
                if (Math.Abs(dx) > GameRoom.VisionCells)
                    continue;
                if (Math.Abs(dy) > GameRoom.VisionCells)
                    continue;
                objects.Add(player);
            }

            foreach (Monster monster in zone.Monsters)
            {
                int dx = monster.CellPos.x - cellPos.x;
                int dy = monster.CellPos.y - cellPos.y;
                if (Math.Abs(dx) > GameRoom.VisionCells)
                    continue;
                if (Math.Abs(dy) > GameRoom.VisionCells)
                    continue;
                objects.Add(monster);
            }

            foreach (Projectile projectile in zone.Projectiles)
            {
                int dx = projectile.CellPos.x - cellPos.x;
                int dy = projectile.CellPos.y - cellPos.y;
                if (Math.Abs(dx) > GameRoom.VisionCells)
                    continue;
                if (Math.Abs(dy) > GameRoom.VisionCells)
                    continue;
                objects.Add(projectile);
            }
        }

        return objects;
    }

    public void Update()
    {
        if (Owner == null || Owner.Room == null)
            return;

        HashSet<GameObject> currentObjects = GatherObjects();

        List<GameObject> added = currentObjects.Except(PreviousObjects).ToList();
        if(added.Count > 0)
        {
            S_Spawn spawnPacket = new S_Spawn();

            foreach(GameObject gameObject in added)
            {
                ObjectInfo info = new ObjectInfo();
                info.MergeFrom(gameObject.Info);
                spawnPacket.Objects.Add(info);
            }

            Owner.Session.Send(spawnPacket);
        }

        List<GameObject> removed = PreviousObjects.Except(currentObjects).ToList();
        if (removed.Count > 0)
        {
            S_Despawn despawnPacket = new S_Despawn();

            foreach (GameObject gameObject in removed)
            {
                despawnPacket.ObjectIds.Add(gameObject.Id);
            }

            Owner.Session.Send(despawnPacket);
        }

        PreviousObjects = currentObjects;

        Owner.Room.PushAfter(100, Update);
    }
}
{% endhighlight %}

* Server\Game\Room\Zone.cs
{% highlight C# %}
public class Zone
{
    public int IndexY { get; private set; }
    public int IndexX { get; private set; }

    public HashSet<Player> Players { get; set; } = new HashSet<Player>();
    public HashSet<Monster> Monsters { get; set; } = new HashSet<Monster>();
    public HashSet<Projectile> Projectiles { get; set; } = new HashSet<Projectile>();

    public Zone(int y, int x)
    {
        IndexY = y;
        IndexX = x;
    }

    public void Remove(GameObject gameObject)
    {
        GameObjectType type = ObjectManager.GetObjectTypeById(gameObject.Id);

        switch (type)
        {
            case GameObjectType.Player:
                Players.Remove((Player)gameObject);
                break;
            case GameObjectType.Monster:
                Monsters.Remove((Monster)gameObject);
                break;
            case GameObjectType.Projectile:
                Projectiles.Remove((Projectile)gameObject);
                break;
        }
    }

    public Player FindOnePlayer(Func<Player, bool> condition)
    {
        foreach(Player player in Players)
        {
            if (condition.Invoke(player))
                return player;

        }

        return null;
    }

    public List<Player> FinaAllPlayers(Func<Player, bool> condition)
    {
        List<Player> findList = new List<Player>();

        foreach (Player player in Players)
        {
            if (condition.Invoke(player))
                findList.Add(player);

        }

        return findList;
    }
}
{% endhighlight %}

* Server\Game\Session\ClientSession_PreGame.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  ...
  public void HandleEnterGame(C_EnterGame enterGamePacket)
  {
    ...
    GameLogic.Instance.Push(() =>
    {
      GameRoom room = GameLogic.Instance.Find(1);
      room.Push(room.EnterGame, MyPlayer, true);
    });
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
    ConfigManager.LoadConfig();
    DataManager.LoadData();

    GameLogic.Instance.Push(() => {	GameLogic.Instance.Add(1); });

    // DNS (Domain Name System)
    string host = Dns.GetHostName();
    IPHostEntry ipHost = Dns.GetHostEntry(host);
    IPAddress ipAddr = ipHost.AddressList[0];
    IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

    _listener.Init(endPoint, () => { return SessionManager.Instance.Generate(); });
    Console.WriteLine("Listening...");

    // DbTask
    {
      Thread t = new Thread(DbTask);
      t.Name = "DB";
      t.Start();
          }

    // NetworkTask
    {
      Thread t = new Thread(NetworkTask);
      t.Name = "Network Send";
      t.Start();
    }

    // GameLogicTask
    Thread.CurrentThread.Name = "GameLogic";
    GameLogicTask();
  }
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Zone.mp4" frameborder="0"> </iframe>

## Massive Structure Final

* Multi Player
  - [File]-[Build Settings]-[Player Settings]
  - set `Api Compatibility Level` to `.Net 4.x`
  - This is because of `NewtonJson` Package

<figure>
  <a href="/assets/img/posts/unity_mmocontents/23.jpg"><img src="/assets/img/posts/unity_mmocontents/23.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/24.jpg"><img src="/assets/img/posts/unity_mmocontents/24.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Map
  - change the map more bigger
  - [Tool]-[GenerateMap]

<figure>
  <a href="/assets/img/posts/unity_mmocontents/28.jpg"><img src="/assets/img/posts/unity_mmocontents/28.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Client\Assets\Scripts\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
  ...
  public static void S_ConnectedHandler(PacketSession session, IMessage packet)
	{
		Debug.Log("S_ConnectedHandler");
		C_Login loginPacket = new C_Login();

		string path = Application.dataPath;
		loginPacket.UniqueId = path.GetHashCode().ToString();
		Managers.Network.Send(loginPacket);
	}
  ...

  public static void S_PingHandler(PacketSession session, IMessage packet)
    {
		C_Pong pongPacket = new C_Pong();
		Debug.Log("[Server] PingCheck");
		Managers.Network.Send(pongPacket);
    }
}
{% endhighlight %}

* Server\Game\Object\Monster.cs
{% highlight C# %}
public class Monster : GameObject
{
  ...
  protected virtual void UpdateMoving()
  {
    ...
    List<Vector2Int> path = Room.Map.FindPath(CellPos, _target.CellPos, checkObjects: true);
    if (path.Count < 2 || path.Count > _chaseCellDist)
    {
      _target = null;
      State = CreatureState.Idle;
      BroadcastMove();
      return;
    }
    ...
  }
  ...
}
{% endhighlight %}

* Server\Game\Object\Monster.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  ...
  public void Init(int mapId, int zoneCells)
  {
    Map.LoadMap(mapId);

    // Zone
    ZoneCells = zoneCells;
    int countY = (Map.SizeY + zoneCells - 1) / zoneCells;
    int countX = (Map.SizeX + zoneCells - 1) / zoneCells;
    Zones = new Zone[countY, countX];

    for(int y=0; y<countY; y++)
          {
      for(int x=0; x<countX; x++)
              {
        Zones[y, x] = new Zone(y, x);
              }
          }

    // TEMP
    for(int i=0; i<500; i++)
          {
      Monster monster = ObjectManager.Instance.Add<Monster>();
      monster.Init(1);
      EnterGame(monster, randomPos: true);
    }
  }
  ...
}
{% endhighlight %}

* Server\Game\Room\Map.cs
{% highlight C# %}
...
public class Map
{
  ...
  public List<Vector2Int> FindPath(Vector2Int startCellPos, Vector2Int destCellPos, bool checkObjects = true, int maxDist = 10)
  {
    ...
    for (int i = 0; i < _deltaY.Length; i++)
    {
      Pos next = new Pos(node.Y + _deltaY[i], node.X + _deltaX[i]);

      // Skip if it is too far
      if (Math.Abs(pos.Y - next.Y) + Math.Abs(pos.X - next.X) > maxDist)
        continue;
      ...
    }
  }
  ...

  List<Vector2Int> CalcCellPathFromParent(Dictionary<Pos, Pos> parent, Pos dest)
  {
    List<Vector2Int> cells = new List<Vector2Int>();

    if (parent.ContainsKey(dest) == false)
    {
      Pos best = new Pos();
      int bestDist = Int32.MaxValue;

      foreach(Pos pos in parent.Keys)
      {
        int dist = Math.Abs(dest.X - pos.X) + Math.Abs(dest.Y - pos.Y);

        if(dist < bestDist)
        {
          best = pos;
          bestDist = dist;
        }
      }

      dest = best;
    }

    {
      Pos pos = dest;

      while (parent[pos] != pos)
      {
        cells.Add(Pos2Cell(pos));
        pos = parent[pos];
      }
      cells.Add(Pos2Cell(pos));
      cells.Reverse();
    }

    return cells;
  }
  ...
}
{% endhighlight %}

* Server\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
  ...
  public static void C_PongHandler(PacketSession session, IMessage packet)
    {
		ClientSession clientSession = (ClientSession)session;
		clientSession.HandlePong();
    }
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  ...
  long _pingpongTick = 0;
  public void Ping()
  {
    if(_pingpongTick > 0)
    {
      long delta = (System.Environment.TickCount64 - _pingpongTick);
      if(delta > 30 * 1000)
      {
        Console.WriteLine("Disconnected by PingCheck");
        Disconnect();
        return;
      }
    } 

    S_Ping pingPacket = new S_Ping();
    Send(pingPacket);

    GameLogic.Instance.PushAfter(5000, Ping);
  }

  public void HandlePong()
  {
    _pingpongTick = System.Environment.TickCount64;
  }
  ...

  public override void OnConnected(EndPoint endPoint)
  {
    Console.WriteLine($"OnConnected : {endPoint}");

    {
      S_Connected connectedPacket = new S_Connected();
      Send(connectedPacket);
    }

    GameLogic.Instance.PushAfter(5000, Ping);
  }
  ...

  public override void OnDisconnected(EndPoint endPoint)
  {
    GameLogic.Instance.Push(() =>
    {
      if (MyPlayer != null)
        return;

      GameRoom room = GameLogic.Instance.Find(1);
      room.Push(room.LeaveGame, MyPlayer.Info.ObjectId);
    });
    ...
  }
  ...
}
{% endhighlight %}

### Test

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-Multi.mp4" frameborder="0"> </iframe>

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/26.jpg"><img src="/assets/img/posts/unity_mmocontents/26.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/27.jpg"><img src="/assets/img/posts/unity_mmocontents/27.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

# Dummy Client

* Create Console Application in Server Solution
  - name is DummyClient
  - set start project `Server` and `DummyClient`

* Common\protoc-3.12.3-win64\bin\GenProto.bat
  - add DummyClient to make `Protocol.cs` and `ClientPacketManager.cs`

{% highlight bat %}
protoc.exe -I=./ --csharp_out=./ ./Protocol.proto 
IF ERRORLEVEL 1 PAUSE

START ../../../Server/PacketGenerator/bin/PacketGenerator.exe ./Protocol.proto
XCOPY /Y Protocol.cs "../../../Client/Assets/Scripts/Packet"
XCOPY /Y Protocol.cs "../../../Server/DummyClient/Packet"
COPY /Y Protocol.cs "../../../Server/Server/Packet"
XCOPY /Y ClientPacketManager.cs "../../../Client/Assets/Scripts/Packet"
XCOPY /Y ClientPacketManager.cs "../../../Server/DummyClient/Packet"
XCOPY /Y ServerPacketManager.cs "../../../Server/Server/Packet"
{% endhighlight %}
  
  - run GenProto.bat

* DummyClient\Packet\PacketHandler.cs
{% highlight C# %}
class PacketHandler
{
	// Step 4
	public static void S_EnterGameHandler(PacketSession session, IMessage packet)
	{
		S_EnterGame enterGamePacket = packet as S_EnterGame;
	}

	public static void S_LeaveGameHandler(PacketSession session, IMessage packet)
	{
		S_LeaveGame leaveGameHandler = packet as S_LeaveGame;
	}

	public static void S_SpawnHandler(PacketSession session, IMessage packet)
	{
		S_Spawn spawnPacket = packet as S_Spawn;
	}

	public static void S_DespawnHandler(PacketSession session, IMessage packet)
	{
		S_Despawn despawnPacket = packet as S_Despawn;
	}

	public static void S_MoveHandler(PacketSession session, IMessage packet)
	{
		S_Move movePacket = packet as S_Move;
	}

	public static void S_SkillHandler(PacketSession session, IMessage packet)
	{
		S_Skill skillPacket = packet as S_Skill;
	}

	public static void S_ChangeHpHandler(PacketSession session, IMessage packet)
	{
		S_ChangeHp changePacket = packet as S_ChangeHp;
	}

	public static void S_DieHandler(PacketSession session, IMessage packet)
	{
		S_Die diePacket = packet as S_Die;
	}

	// Step 1
	public static void S_ConnectedHandler(PacketSession session, IMessage packet)
	{
		C_Login loginPacket = new C_Login();

		ServerSession serverSession = (ServerSession)session;
		loginPacket.UniqueId = $"DummyClient_{serverSession.DummyId.ToString("0000")}";
		serverSession.Send(loginPacket);
	}

	// Step 2
	public static void S_LoginHandler(PacketSession session, IMessage packet)
	{
		S_Login loginPacket = (S_Login)packet;
		ServerSession serverSession = (ServerSession)session;

		if (loginPacket.Players == null || loginPacket.Players.Count == 0)
		{
			C_CreatePlayer createPacket = new C_CreatePlayer();
			createPacket.Name = $"Player_{serverSession.DummyId.ToString("0000")}";
			serverSession.Send(createPacket);
		}
		else
		{
			// First Login
			LobbyPlayerInfo info = loginPacket.Players[0];
			C_EnterGame enterGamePacket = new C_EnterGame();
			enterGamePacket.Name = info.Name;
			serverSession.Send(enterGamePacket);
		}
	}

	// Step 3
	public static void S_CreatePlayerHandler(PacketSession session, IMessage packet)
	{
		S_CreatePlayer createOkPacket = (S_CreatePlayer)packet;
		ServerSession serverSession = (ServerSession)session;

		if (createOkPacket.Player == null)
		{
			// Skip
		}
		else
		{
			C_EnterGame enterGamePacket = new C_EnterGame();
			enterGamePacket.Name = createOkPacket.Player.Name;
			serverSession.Send(enterGamePacket);
		}
	}

	public static void S_ItemListHandler(PacketSession session, IMessage packet)
	{
		S_ItemList itemList = (S_ItemList)packet;
	}

	public static void S_AddItemHandler(PacketSession session, IMessage packet)
	{
	}

	public static void S_EquipItemHandler(PacketSession session, IMessage packet)
	{
	}

	public static void S_ChangeStatHandler(PacketSession session, IMessage packet)
	{
		S_ChangeStat itemList = (S_ChangeStat)packet;
	}

	public static void S_PingHandler(PacketSession session, IMessage packet)
	{
		C_Pong pongPacket = new C_Pong();
	}
}
{% endhighlight %}

* DummyClient\Session\SessionManager.cs
{% highlight C# %}
public class SessionManager
{
    public static SessionManager Instance { get; } = new SessionManager();

    HashSet<ServerSession> _sessions = new HashSet<ServerSession>();
    object _lock = new object();
    int _dummyId = 1;

    public ServerSession Generate()
    {
        lock (_lock)
        {
            ServerSession session = new ServerSession();
            session.DummyId = _dummyId;
            _dummyId++;

            _sessions.Add(session);
            Console.WriteLine($"Connected({_sessions.Count}) Players");
            return session;
        }
    }

    public void Remove(ServerSession session)
    {
        lock (_lock)
        {
            _sessions.Remove(session);
            Console.WriteLine($"Connected({_sessions.Count}) Players");
        }
    }
}   
{% endhighlight %}

* DummyClient\Session\ServerSession.cs
{% highlight C# %}
public class ServerSession : PacketSession
{
	public int DummyId { get; set; }

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

	public override void OnConnected(EndPoint endPoint)	{ }

	public override void OnDisconnected(EndPoint endPoint)	{	}

	public override void OnRecvPacket(ArraySegment<byte> buffer)
	{
		PacketManager.Instance.OnRecvPacket(this, buffer);
	}

	public override void OnSend(int numOfBytes)	{	}
}
{% endhighlight %}

* DummyClient\Program.cs
{% highlight C# %}
class Program
{
  static int DummyClientCount { get; } = 50;

  static void Main(string[] args)
  {
    Thread.Sleep(3000);

    string host = Dns.GetHostName();
    IPHostEntry ipHost = Dns.GetHostEntry(host);
    IPAddress ipAddr = ipHost.AddressList[0];
    IPEndPoint endPoint = new IPEndPoint(ipAddr, 7777);

    Connector connector = new Connector();

    connector.Connect(endPoint,
        () => { return SessionManager.Instance.Generate(); },
        Program.DummyClientCount);

    while (true) 
    {
        Thread.Sleep(10000);
    }
  }
}
{% endhighlight %}

* Server\DB\AppDbContext.cs
{% highlight C# %}
public class AppDbContext : DbContext
{
  ...
  protected override void OnConfiguring(DbContextOptionsBuilder options)
  {
    options
      //.UseLoggerFactory(_logger)
      .UseSqlServer(ConfigManager.Config == null ? _connectionString : ConfigManager.Config.connectionString);
  }
  ...
}
{% endhighlight %}

* Server\DB\DbTransaction.cs
  - Remove Logs

{% highlight C# %}
public partial class DbTransaction : JobSerializer
{
  ...
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
          //room.Push(() => Console.WriteLine($"Hp Saved({playerDb.Hp})"));
        }
      }
    });			
  }
  ...
  public static void SavePlayerStatus_Step3(int hp)
  {
    //Console.WriteLine($"Hp Saved({hp})");
  }
  ...
  }
{% endhighlight %}

* Server\Game\Object\Arrow.cs
  - Remove Logs

{% highlight C# %}
public class Arrow : Projectile
{
  ...
  public override void Update()
  {
    ...
    if (Room.Map.ApplyMove(this, destPos, collision: false))
    {
      ...
      //Console.WriteLine("Move Arrow");
    }
  ...
}
{% endhighlight %}

* Server\Game\Object\Monster.cs
{% highlight C# %}
public class Monster : GameObject
{
  ...
  protected virtual void UpdateIdle()
  {
    if (_nextSearchTick > Environment.TickCount64)
      return;
    _nextSearchTick = Environment.TickCount64 + 1000;

    Player target = Room.FindClosestPlayer(CellPos, _searchCellDist);

    if (target == null)
      return;

    _target = target;
    State = CreatureState.Moving;
  }
  ...
}
{% endhighlight %}

* Server\Room\GameRoom.cs
{% highlight C# %}
public partial class GameRoom : JobSerializer
{
  ...
  public Zone GetZone(Vector2Int cellPos)
  {
    int x = (cellPos.x - Map.MinX) / ZoneCells;
    int y = (Map.MaxY - cellPos.y) / ZoneCells;
    return GetZone(y, x);
  }

  public Zone GetZone(int indexY, int indexX)
  {
    if (indexX < 0 || indexX >= Zones.GetLength(1))
      return null;
    if (indexY < 0 || indexY >= Zones.GetLength(0))
      return null;

    return Zones[indexY, indexX];
  }
  ...

  Player FindPlayer(Func<GameObject, bool> condition)
  {
    foreach (Player player in _players.Values)
    {
      if (condition.Invoke(player))
        return player;
    }

    return null;
  }

  public Player FindClosestPlayer(Vector2Int pos, int range)
  {
    List<Player> players = GetAdjacentPlayers(pos, range);

    players.Sort((left, right) =>
    {
      int leftDist = (left.CellPos - pos).cellDistFromZero;
      int rightDist = (right.CellPos - pos).cellDistFromZero;
      return leftDist - rightDist;
    });

    foreach(Player player in players)
    {
      List<Vector2Int> path = Map.FindPath(pos, player.CellPos, checkObjects: true);
      if (path.Count < 2 || path.Count > range)
        continue;

      return player;
    }

    return null;
  }
  ...

  public List<Player> GetAdjacentPlayers(Vector2Int pos, int range)
  {
    List<Zone> zones = GetAdjacentZones(pos, range);
    return zones.SelectMany(z => z.Players).ToList();
  }

  public List<Zone> GetAdjacentZones(Vector2Int cellPos, int range = GameRoom.VisionCells)
  {
    HashSet<Zone> zones = new HashSet<Zone>();

    int maxY = cellPos.y + range;
    int minY = cellPos.y - range;
    int maxX = cellPos.x + range;
    int minx = cellPos.x - range;

    // left top
    Vector2Int leftTop = new Vector2Int(minx, maxY);

    int minIndexY = (Map.MaxY - leftTop.y) / ZoneCells;
    int minIndexX = (leftTop.x - Map.MinX) / ZoneCells;

    // right bottom
    Vector2Int rightBot = new Vector2Int(maxX, minY);

    int maxIndexY = (Map.MaxY - rightBot.y) / ZoneCells;
    int maxIndexX = (rightBot.x - Map.MinX) / ZoneCells;

    for(int x = minIndexX; x<=maxIndexX; x++)
    {
      for(int y=minIndexY; y<=maxIndexY; y++)
      {
        Zone zone = GetZone(y, x);
        if (zone == null)
          continue;

        zones.Add(zone);
      }
    }

    int[] delta = new int[2] { -range, +range };
    foreach(int dy in delta)
    {
      foreach(int dx in delta)
      {
        int y = cellPos.y + dy;
        int x = cellPos.x + dx;
        Zone zone = GetZone(new Vector2Int(x, y));
        if (zone == null)
          continue;

        zones.Add(zone);
      }
    }

    return zones.ToList();
  }
  ...
}
{% endhighlight %}

* Server\Session\ClientSession.cs
{% highlight C# %}
public partial class ClientSession : PacketSession
{
  ...
  // Packet Merge Send
  int _reservedSendBytes = 0;
  long _lastSendTick = 0;
  ...
  
  public void Send(IMessage packet)
  {
    string msgName = packet.Descriptor.Name.Replace("_", string.Empty);
    MsgId msgId = (MsgId)Enum.Parse(typeof(MsgId), msgName);
    ushort size = (ushort)packet.CalculateSize();
    byte[] sendBuffer = new byte[size + 4];
    Array.Copy(BitConverter.GetBytes((ushort)(size + 4)), 0, sendBuffer, 0, sizeof(ushort));
    Array.Copy(BitConverter.GetBytes((ushort)msgId), 0, sendBuffer, 2, sizeof(ushort));
    Array.Copy(packet.ToByteArray(), 0, sendBuffer, 4, size);

          lock (_lock)
          {
      _reserveQueue.Add(sendBuffer);
      _reservedSendBytes += sendBuffer.Length;
    }
  }

  public void FlushSend()
  {
    List<ArraySegment<byte>> sendList = null;

    lock (_lock)
    {
      // already past 0.1 sec or too many packets are merged(1 million bytes)
      long delta = (System.Environment.TickCount64 - _lastSendTick);
      if (delta < 100 && _reservedSendBytes < 10000)
        return;

      // Packet Merge Send
      _reservedSendBytes = 0;
      _lastSendTick = System.Environment.TickCount64;

      if (_reserveQueue.Count == 0)
        return;

      sendList = _reserveQueue;
      _reserveQueue = new List<ArraySegment<byte>>();
    }

    Send(sendList);
  }

  public override void OnConnected(EndPoint endPoint)
  {
    //Console.WriteLine($"OnConnected : {endPoint}");

    {
      S_Connected connectedPacket = new S_Connected();
      Send(connectedPacket);
    }

    GameLogic.Instance.PushAfter(5000, Ping);
  }
  ...

  public override void OnDisconnected(EndPoint endPoint)
  {
    GameLogic.Instance.Push(() =>
    {
      if (MyPlayer != null)
        return;

      GameRoom room = GameLogic.Instance.Find(1);
      room.Push(room.LeaveGame, MyPlayer.Info.ObjectId);
    });
    
    SessionManager.Instance.Remove(this);
  }
  ...
}
{% endhighlight %}

* Server\Session\SessionManager.cs
{% highlight C# %}
class SessionManager
{
  ...
  public ClientSession Generate()
  {
    lock (_lock)
    {
      int sessionId = ++_sessionId;

      ClientSession session = new ClientSession();
      session.SessionId = sessionId;
      _sessions.Add(sessionId, session);

      Console.WriteLine($"Connected({_sessions.Count}) Players");

      return session;
    }
  }
  ...
  public void Remove(ClientSession session)
  {
    lock (_lock)
    {
      _sessions.Remove(session.SessionId);
              Console.WriteLine($"Connected({_sessions.Count}) Players");
    }
  }
}
{% endhighlight %}

* ServerCore\Connector.cs
{% highlight C# %}
public class Connector
{
  ...
  public void Connect(IPEndPoint endPoint, Func<Session> sessionFactory, int count = 1)
  {
    for (int i = 0; i < count; i++)
    {
      Socket socket = new Socket(endPoint.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
      _sessionFactory = sessionFactory;

      SocketAsyncEventArgs args = new SocketAsyncEventArgs();
      args.Completed += OnConnectCompleted;
      args.RemoteEndPoint = endPoint;
      args.UserToken = socket;

      RegisterConnect(args);

      //TEMP
      Thread.Sleep(10);
    }
  }

  void RegisterConnect(SocketAsyncEventArgs args)
  {
    Socket socket = args.UserToken as Socket;
    if (socket == null)
      return;
    try
    {
      bool pending = socket.ConnectAsync(args);
      if (pending == false)
        OnConnectCompleted(null, args);
    }
    catch(Exception e)
    {
      Console.WriteLine(e);
    }
  }

  void OnConnectCompleted(object sender, SocketAsyncEventArgs args)
  {
    try
    {
      if (args.SocketError == SocketError.Success)
      {
        Session session = _sessionFactory.Invoke();
        session.Start(args.ConnectSocket);
        session.OnConnected(args.RemoteEndPoint);
      }
      else
      {
        Console.WriteLine($"OnConnectCompleted Fail: {args.SocketError}");
      }
    }
    catch(Exception e)
    {
      Console.WriteLine(e);
    }
  }
}
{% endhighlight %}

* ServerCore\Listener.cs
{% highlight C# %}
public class Listener
{
  ...
  void RegisterAccept(SocketAsyncEventArgs args)
  {
    args.AcceptSocket = null;

          try
          {
      bool pending = _listenSocket.AcceptAsync(args);
      if (pending == false)
        OnAcceptCompleted(null, args);
    }
    catch(Exception e)
          {
              Console.WriteLine(e);
          }
  }

  void OnAcceptCompleted(object sender, SocketAsyncEventArgs args)
  {
          try
          {
      if (args.SocketError == SocketError.Success)
      {
        Session session = _sessionFactory.Invoke();
        session.Start(args.AcceptSocket);
        session.OnConnected(args.AcceptSocket.RemoteEndPoint);
      }
      else
        Console.WriteLine(args.SocketError.ToString());
    }
          catch(Exception e)
          {
              Console.WriteLine(e);
          }

    RegisterAccept(args);
  }
}
{% endhighlight %}

* ServerCore\Session.cs
{% highlight C# %}
public abstract class PacketSession : Session
{
  ...
  public sealed override int OnRecv(ArraySegment<byte> buffer)
  {
    int processLen = 0;

    while (true)
    {
      if (buffer.Count < HeaderSize)
        break;

      ushort dataSize = BitConverter.ToUInt16(buffer.Array, buffer.Offset);
      if (buffer.Count < dataSize)
        break;

      OnRecvPacket(new ArraySegment<byte>(buffer.Array, buffer.Offset, dataSize));

      processLen += dataSize;
      buffer = new ArraySegment<byte>(buffer.Array, buffer.Offset + dataSize, buffer.Count - dataSize);
    }

    return processLen;
  }

  public abstract void OnRecvPacket(ArraySegment<byte> buffer);
}
...
{% endhighlight %}

### Test

<figure class="half">
  <a href="/assets/img/posts/unity_mmocontents/29.jpg"><img src="/assets/img/posts/unity_mmocontents/29.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/30.jpg"><img src="/assets/img/posts/unity_mmocontents/30.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/unity_mmocontents/MMO-Contents-DummyClient.mp4" frameborder="0"> </iframe>

# Account

## Server

* Create new Asp.Net Core Web Application
  - Application name is `AccountServer`
  - Remove `WeatherForecast~`

<figure>
  <a href="/assets/img/posts/unity_mmocontents/31.jpg"><img src="/assets/img/posts/unity_mmocontents/31.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Download Nuget Packeage 
  - Microsoft.EntityFrameworkCore.Dsign
  - Microsoft.EntityFrameworkCore.SqlServer

<figure>
  <a href="/assets/img/posts/unity_mmocontents/37.jpg"><img src="/assets/img/posts/unity_mmocontents/37.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* AccountServer\DB\AppDbContext.cs
{% highlight C# %}
public class AppDbContext : DbContext
{
    public DbSet<AccountDb> Accounts { get; set; }

    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {

    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        builder.Entity<AccountDb>()
            .HasIndex(a => a.AccountName)
            .IsUnique();
    }
}
{% endhighlight %}

* AccountServer\DB\DataModel.cs
{% highlight C# %}
[Table("Account")]
	public class AccountDb
	{
		public int AccountDbId { get; set; }
		public string AccountName { get; set; }
		public string Password { get; set; }

	}
{% endhighlight %}

* AccountServer\DB\WebPacket.cs
{% highlight C# %}
// Client -> Server
public class CreateAccountPacketReq
{
    public string AccountName { get; set; }
    public string Password { get; set; }
}

// Server -> Client
public class CreateAccountPacketRes
{
    public bool CreateOk { get; set; }
}

public class LoginAccountPacketReq
{
    public string AccountName { get; set; }
    public string Password { get; set; }
}

public class ServerInfo
{
    public string Name { get; set; }
    public string Ip { get; set; }
    public int CrowdedLevel { get; set; }
}

public class LoginAccountPacketRes
{
    public bool LoginOk { get; set; }
    public List<ServerInfo> ServerList { get; set; } = new List<ServerInfo>();
}
{% endhighlight %}

* AccountServer\Properties\launchSettings.json
{% highlight json %}
...
  "AccountServer": {
      "commandName": "Project",
      "launchBrowser": false,
      "launchUrl": "api/account",
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
...
{% endhighlight %}

* Create Database
  - name is `AccountDB`
  - copy `ConnectionStrings` and paste in `appsettings.json`

* AccountServer\Properties\appsettings.json
{% highlight json %}
...
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=AccountDB;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False"
  },
...
{% endhighlight %}

* AccountServer\Properties\Extensions.cs
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

* AccountServer\Startup.cs
{% highlight C# %}
public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddControllers().AddJsonOptions(options=>
      {
          options.JsonSerializerOptions.PropertyNamingPolicy = null;
          options.JsonSerializerOptions.DictionaryKeyPolicy = null;
      });

      services.AddDbContext<AppDbContext>(options =>
          options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
  }
  ...
}
{% endhighlight %}

* Migration and Update Database

<figure>
  <a href="/assets/img/posts/unity_mmocontents/32.jpg"><img src="/assets/img/posts/unity_mmocontents/32.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* Create API Controller
  - name is `AccountController.cs`

<figure>
  <a href="/assets/img/posts/unity_mmocontents/33.jpg"><img src="/assets/img/posts/unity_mmocontents/33.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

* AccountServer\Controller\AccountController.cs
{% highlight C# %}
[Route("api/[controller]")]
[ApiController]
public class AccountController : ControllerBase
{
    AppDbContext _context;

    public AccountController(AppDbContext context)
    {
        _context = context;
    }

    [HttpPost]
    [Route("create")]
    public CreateAccountPacketRes CreateAccount([FromBody] CreateAccountPacketReq req)
    {
        CreateAccountPacketRes res = new CreateAccountPacketRes();

        AccountDb account = _context.Accounts
            .AsNoTracking()
            .Where(a => a.AccountName == req.AccountName)
            .FirstOrDefault();

        if(account == null)
        {
            _context.Accounts.Add(new AccountDb()
            {
                AccountName = req.AccountName,
                Password = req.Password
            });

            bool success = _context.SaveChangesEx();
            res.CreateOk = success;
        }
        else
        {
            res.CreateOk = false;
        }

        return res;
    }

    [HttpPost]
    [Route("login")]
    public LoginAccountPacketRes LoginAccount([FromBody] LoginAccountPacketReq req)
    {
        LoginAccountPacketRes res = new LoginAccountPacketRes();

        AccountDb account = _context.Accounts
            .AsNoTracking()
            .Where(a => a.AccountName == req.AccountName && a.Password == req.Password)
            .FirstOrDefault();

        if(account == null)
        {
            res.LoginOk = false;
        }
        else
        {
            res.LoginOk = true;

            // TODO 서버 목록
            res.ServerList = new List<ServerInfo>()
            {
                new ServerInfo(){Name = "Deforejue", Ip = "127.0.0.1", CrowdedLevel = 0 },
                new ServerInfo(){Name = "Atun", Ip = "127.0.0.1", CrowdedLevel = 3 }
            };
        }

        return res;
    }
}
{% endhighlight %}

* Client\Assets\Scripts\Managers\Contents\WebManager.cs
{% highlight C# %}
public class WebManager
{
    public string BaseUrl { get; set; } = "https://localhost:5001/api";

    public void SendPostRequest<T>(string url, object obj, Action<T> res)
    {
        Managers.Instance.StartCoroutine(CoSendWebRequest(url, UnityWebRequest.kHttpVerbPOST, obj, res));
    }
    // T is respon object type
    IEnumerator CoSendWebRequest<T>(string url, string method, object obj, Action<T> res)
    {
        string sendUrl = $"{BaseUrl}/{url}";

        byte[] jsonBytes = null;
        if(obj != null)
        {
            string jsonStr = JsonUtility.ToJson(obj);
            jsonBytes = Encoding.UTF8.GetBytes(jsonStr);
        }

        using (var uwr = new UnityWebRequest(sendUrl, method))
        {
            uwr.uploadHandler = new UploadHandlerRaw(jsonBytes);
            uwr.downloadHandler = new DownloadHandlerBuffer();
            uwr.SetRequestHeader("Content-Type", "application/json");

            yield return uwr.SendWebRequest();

            if(uwr.isNetworkError || uwr.isHttpError)
            {
                Debug.Log(uwr.error);
            }
            else
            {
                T resObj = JsonUtility.FromJson<T>(uwr.downloadHandler.text);
                res.Invoke(resObj);
            }

        }
    }
}
{% endhighlight %}

* Client\Assets\Scripts\Managers\Managers.cs
{% highlight C# %}
public class Managers : MonoBehaviour
{
    ...

    #region Contents
    ...
    public static WebManager Web { get { return Instance._web ; } }
	  #endregion
    ...
}
{% endhighlight %}


* Client\Assets\Scripts\Scenes\GameScene.cs
{% highlight C# %}
public class GameScene : BaseScene
{
    ...

    protected override void Init()
    {
        base.Init();

        SceneType = Define.Scene.Game;

        // Temp
        Managers.Web.BaseUrl = "https://localhost:5001/api";
        WebPacket.SendCreateAccount("Hanna", "1234");

        ...
    }
    ...
}
{% endhighlight %}

* Client\Assets\Scripts\Web\WebPacket.cs
{% highlight C# %}
public class CreateAccountPacketReq
{
    public string AccountName;
    public string Password;
}

public class CreateAccountPacketRes
{
    public bool CreateOk;
}

public class LoginAccountPacketReq
{
    public string AccountName;
    public string Password;
}

public class ServerInfo
{
    public string Name;
    public string Ip;
    public int CrowdedLevel;
}

public class LoginAccountPacketRes
{
    public bool LoginOk;
    public List<ServerInfo> ServerList = new List<ServerInfo>();
}

public class WebPacket
{
    public static void SendCreateAccount(string account, string password)
    {
        CreateAccountPacketReq packet = new CreateAccountPacketReq()
        {
            AccountName = account,
            Password = password
        };

        Managers.Web.SendPostRequest<CreateAccountPacketRes>("account/create", packet, (res) =>
        {
            Debug.Log(res.CreateOk);
        });
    }

    public static void SendLoginAccount(string account, string password)
    {
        LoginAccountPacketReq packet = new LoginAccountPacketReq()
        {
            AccountName = account,
            Password = password
        };

        Managers.Web.SendPostRequest<LoginAccountPacketRes>("account/login", packet, (res) =>
        {
            Debug.Log(res.LoginOk);
        });
    }
}
{% endhighlight %}

### Test

<figure class="third">
  <a href="/assets/img/posts/unity_mmocontents/34.jpg"><img src="/assets/img/posts/unity_mmocontents/34.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/35.jpg"><img src="/assets/img/posts/unity_mmocontents/35.jpg"></a>
  <a href="/assets/img/posts/unity_mmocontents/36.jpg"><img src="/assets/img/posts/unity_mmocontents/36.jpg"></a>
	<figcaption>Unity MMO Contents</figcaption>
</figure>

[Download](https://github.com/leehuhlee/Unity){: .btn}
