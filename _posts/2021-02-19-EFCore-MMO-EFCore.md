---
layout: post
title: "[EFCore] MMO EFCore"
date: 2021-02-19
excerpt: "Entity Framework Core and ORM"
tags: [Database, Entity Framework Core, ORM]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part8/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part8: Entity Framework Core</a></center>

# linking with DB

## Configuration

* Table name
  - Entity Class name defines Table name
  - for example, the `Item` class makes `Item` table in DB and the `Player` class makes `Player` table in DB
  - you can define table name by annotation, example, `[Table("Item")]`

* Primary Key
  - [ClassName]Id is specified by primary key
  - you can define primary key by annotation, example, `[Key]`

* Foreign Key
  - reference another class
  - Navigational Property
  - when type of the object is outer class type, the object is foreign data
  - for example, type of the `Owner` is `Player`, so `Owner` is foreign data
  - to make foreign key to data, make object name to [ObjectName]Id
  - or just use annotation `[ForeignKey("[ObjectName]")]` or `[ForeignKEy("[ObjectName]Id")]`

* Alternate Key
  - not primary key but it is unique, then it can be alternate key
  - for example, if `Name` is unique, then it can be alternate key in `Player` table

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    public int ItemId { get; set; }
    public int TemplatedId { get; set; }
    public DateTime CreateDate { get; set; }

    public int OwnerId { get; set; }
    public Player Owner { get; set; }
  }

  public class Player
  {
    public int PlayerId { get; set; }
    public string Name { get; set; }
  }
{% endhighlight %}

## EF Core Process 

* step
 - make DbContext 
 - search DbSet<T>
 - analize modeling class, and search column
 - if there is another class which is referenced in modeling class, analize it also
 - call OnModelCreating function(extra setting = override)
 - hold all modeling structure of database in inner memory

* DbSet
  - notice to EFCore
  - ex. there is Items in DB table, and let it reference detail column/ key information on Item class

* DB ConnectionString
  - connect DB
  - include settings, Authorization, and etc

* SqlServer
  - if you use Sql Server, reference `OnConfiguring` method below

* AppDbContext.cs
{% highlight C# %}
  class AppDbContext : DbContext
  {
    public DbSet<Item> Items { get; set; }
    public DbSet<Player> Players { get; set; }

    public const string ConnectionString = "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=EFCoreDb;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
      options.UseSqlServer(ConnectionString);
    }
  }
{% endhighlight %}

## Initialize DB

* check exsisting DB
  - `(db.GetService<IDatabaseCreator>() as RelationalDatabaseCreator).Exists()`

* EnsuredDeleted()
  - Delete Database and ensure it

* EnsuredCreated()
  - Create Database and ensure it

* using{}
  - to avoid `Disposal()`

* DbCommands.cs
{% highlight C# %}
  public static void InitializeDB(bool forceReset = false)
  {
    using (AppDbContext db = new AppDbContext())
    {
      if (!forceReset && (db.GetService<IDatabaseCreator>() as RelationalDatabaseCreator).Exists())
        return;

      db.Database.EnsureDeleted();
      db.Database.EnsureCreated();

      CreateTestData(db);
      Console.WriteLine("DB Initialized");
    }
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/0.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/0.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# CRUD

## C(Create)
* AddRange([ListName])
  - same function with Add([ObjectName])
  - but you can add a bundle of objects in List
  
* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    var hanna = new Player() { Name = "Hanna" };
    var faker = new Player() { Name = "Faker" };
    var deft = new Player() { Name = "Deft" };

    List<Item> items = new List<Item>()
    {
      new Item()
      {
        TemplatedId = 101,
        CreateDate = DateTime.Now,
        Owner = hanna
      },
      new Item()
      {
        TemplatedId = 102,
        CreateDate = DateTime.Now,
        Owner = faker
      },
      new Item()
      {
        TemplatedId = 103,
        CreateDate = DateTime.Now,
        Owner = deft
      }
    };

    db.Items.AddRange(items);
    db.SaveChanges();
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/1.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/1.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/2.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/2.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## R(Read)

* AsNoTracking
  - ReadOnly
  - to skip Tracking Snapshot

* Tracking Snapshot
  - detect data's change
      
* Include
  - Eager Loading
  - load foreign data by foreign key

* DbCommands.cs
{% highlight C# %}
  public static void ReadAll()
  {
    using(var db = new AppDbContext())
    {
      foreach(Item item in db.Items.AsNoTracking().Include(i => i.Owner))
      {
        Console.WriteLine($"TemplatedId({item.TemplatedId}) Owner({item.Owner.Name}) Created({item.CreateDate})");
      }
    }
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/3.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/3.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## U(Update)

* DbCommands.cs
{% highlight C# %}
  // change items of CreateDate of speficied player
  public static void UpdateDate()
  {
    Console.WriteLine("Input Player Name");
    Console.Write("> ");
    string name = Console.ReadLine();

    using(var db = new AppDbContext())
    {
      var items = db.Items.Include(i => i.Owner)
                    .Where(i => i.Owner.Name == name);

      foreach(Item item in items)
      {
        item.CreateDate = DateTime.Now;
      }

      db.SaveChanges();
    }

    ReadAll();
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/4.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/4.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/5.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/5.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>


## D(Delete)

* RemoveRange([ListName])
  - same function with Remove([ObjectName])
  - but you can remove a bundle of objects in List

* DbCommands.cs
{% highlight C# %}
  public static void DeleteItem()
  {
    Console.WriteLine("Input Player Name");
    Console.Write("> ");
    string name = Console.ReadLine();

    using (var db = new AppDbContext())
    {
      var items = db.Items.Include(i => i.Owner)
                    .Where(i => i.Owner.Name == name);

      db.Items.RemoveRange(items);
      db.SaveChanges();
    }

    ReadAll();
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/6.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/6.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/7.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/7.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Relationship

## DB Relationship

* DB relationship modeling
  - 1:1<br>
  - 1:N<br>
    Normally, Foreign Key is N<br>
  - N:N

* DataModel.cs
{% highlight C# %}
  public class Item
  {
    ...
	  public Player Owner { get; set; }
  }

	public class Player
  {
    ...
    public ICollection<Item> Items { get; set; } // 1:N
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void ShowItems()
  {
    Console.WriteLine("Input Player Name");
    Console.Write("> ");
    string name = Console.ReadLine();

    using(var db = new AppDbContext())
    {
      foreach(Player player in db.Players.AsNoTracking().Where(p => p.Name == name).Include(p => p.Items))
      {
        foreach(Item item in player.Items)
        {
          Console.WriteLine($"{item.TemplateId}");
        }
      }
    }
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/8.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/8.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/9.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/9.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/10.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/10.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Loading

## Model change
* DataModel.cs

{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    [ForeignKey("OwnerId")]
    public Player Owner { get; set; }
  }
  
  [Table("Player")]
  public class Player
  {
    // public ICollection<Item> Items { get; set; }
    public Item Item { get; set; }
    public Guild Guild { get; set; }
  }
  
  [Table("Guild")]
  public class Guild
  {
    public int GuildId { get; set; }
    public string GuildName { get; set; }
    public ICollection<Player> Members { get; set; }
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  public DbSet<Player> Players { get; set; }
	public DbSet<Guild> Guilds { get; set; }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    Guild guild = new Guild()
    {
      GuildName = "T1",
      Members = new List<Player>() { hanna, faker, deft }
    };

    db.Guilds.Add(guild);
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/12.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/12.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Eager Loading
* Pros
  - can access DB at once

* Cons 
  - load all data

* DbCommands.cs

{% highlight C# %}
  public static void EagerLoading()
	{
		Console.WriteLine("Input Guild Name");
		Console.Write("> ");
		string name = Console.ReadLine();

		using (var db = new AppDbContext())
		{
			Guild guild = db.Guilds.AsNoTracking()
			            		.Where(g => g.GuildName == name)
					            .Include(g => g.Members)
						            .ThenInclude(p => p.Item)
					            .First();

			foreach (Player player in guild.Members)
			{
				Console.WriteLine($"TemplateId({player.Item.TemplateId}) Owner({player.Name})");
			}
		}
	}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/11.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/11.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Explicit Loading

* Pros
  - can define loading moment

* Cons 
  - cost of DB access

* DbCommands.cs

{% highlight C# %}
  public static void ExplicitLoading()
	{
	  Console.WriteLine("Input Guild Name");
		Console.Write("> ");
		string name = Console.ReadLine();

		using (var db = new AppDbContext())
		{
			Guild guild = db.Guilds
					            .Where(g => g.GuildName == name)
					            .First();

			db.Entry(guild).Collection(g => g.Members).Load();

			foreach (Player player in guild.Members)
			{
				db.Entry(player).Reference(p => p.Item).Load();
			}

			foreach (Player player in guild.Members)
			{
				Console.WriteLine($"TemplateId({player.Item.TemplateId}) Owner({player.Name})");
			}
		}
	}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/13.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/13.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Select Loading

* Pros
  - loading data which is really needed

* Cons
  - need to write in select<br>
    ex. SELECT COUNT(*)

* DbCommends.cs

{% highlight C# %}
  public static void SelectLoading()
	{
		Console.WriteLine("Input Guild Name");
		Console.Write("> ");
		string name = Console.ReadLine();

		using (var db = new AppDbContext())
		{
			var info = db.Guilds
			             .Where(g => g.GuildName == name)
					         .Select(g => new
		              	{
				              Name = g.GuildName,
				              MemberCount = g.Members.Count
			              })
					         .First();

			Console.WriteLine($"GuildName({info.Name}), MemberCount({info.MemberCount})");
		}
	}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/14.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/14.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>
