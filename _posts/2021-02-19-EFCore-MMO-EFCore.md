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
    ...
    [ForeignKey("OwnerId")]
    public Player Owner { get; set; }
  }
  
  [Table("Player")]
  public class Player
  {
    ...
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

# Data Transfer Object(DTO)

## Model change
* DataModel.cs

{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    // [ForeignKey("OwnerId")]
    public int OwnerId { get; set; }
    public Player Owner { get; set; }
  }

  public class GuildDto
  {
    public int GuildId { get; set; }
    public string Name { get; set; }
    public int MemberCount { get; set; }
  }
{% endhighlight %}

## Select Loading
* Select()
  - By DTO, you can make a object instead of `.Select()`
  - and this solution uses LINQ, so it more easier to write and manage

* IEnumerable 
  - LINQ to Object
  - LINQ to XML Query

* IQueryable
  - LINQ to SQL Query

* Extension.cs
{% highlight C# %}
  public static IQueryable<GuildDto> MapGuildToDto(this IQueryable<Guild> guild)
  {
    return guild.Select(g => new GuildDto()
    {
      GuildId = g.GuildId,
      Name = g.GuildName,
      MemberCount = g.Members.Count
    });
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void SelectLoading()
  {
    ...
    using (var db = new AppDbContext())
    {
      var info = db.Guilds
                   .Where(g => g.GuildName == name)
                   .MapGuildToDto()
                   .First();
      ...
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/15.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/15.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# State

## State Type
* Detached
  - No Tracking
  - cannot use SaveChanges()
* Unchanged
  - exist in DB, but not change
  - not changed from SaveChanges()
* Deleted
  - exist in DB, but should be deleted
  - apply deleting from SaveChanges()
* Modified
  - exist in DB, changed in Client
  - apply changing from SaveChanges()
* Added
  - not exist in DB
  - apply adding from SaveChanges

## SaveChanges()
* SaveChanges() 
  - state of added objects is changed to Unchanged
  - manage PK by SQL Identity
  - after adding data, received ID and fill Id property of object
  - reference Relationship and setting FK and link Object Reference

* link existed player
  - first, Tracked Instance
  - then, SaveChanges()

## State
* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    var hanna = new Player() { Name = "Hanna" };
    ...
    
    // 1) Detached
    Console.WriteLine(db.Entry(hanna).State);
    ...
    db.Items.AddRange(items);
    db.Guilds.Add(guild);

    // 2) Added
    Console.WriteLine(db.Entry(hanna).State);
    Console.WriteLine(hanna.PlayerId);    

    db.SaveChanges();

    // link with existed item
    {
      var owner = db.Players.Where(p => p.Name == "Hanna").First();

      Item item = new Item()
      {
        TemplateId = 300,
        CreateDate = DateTime.Now,
        Owner = owner
      };
      db.Items.Add(item);
      db.SaveChanges();
    }

    // 3) Unchanged
    Console.WriteLine(db.Entry(hanna).State);
    Console.WriteLine(hanna.PlayerId);
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/16.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/16.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Update

## Update 3 Step
* Tracked Entity
* change Property of Entity Class(set)
* call SaveChanges

## Update
* call SaveChanges
  - call DetectChanges innerly
* compare first Snapshot with current Snapshot in DetectChange
* in SQL
{% highlight SQL %}
  SELECT TOP(2) GuildId, GuildName
  FROM [Guilds]
  WHERE GuildName = N'T1';

  SET NOCOUNT ON;
         
  UPDATE [Guilds]
  SET GuildName = @p0
  WHERE GuildID = @p1;

  SELECT @@ROWCOUNT;
{% endhighlight %}

{% highlight C# %}
  public static void UpdateTest()
  {
    using(AppDbContext db = new AppDbContext())
    {
      var guild = db.Guilds.Single(g => g.GuildName == "T1");

      guild.GuildName = "DWG";

      db.SaveChanges();
    }
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/17.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/17.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/18.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/18.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Connected vs Disconnected Update

## Disconnected
* Disconnected
  - `Update` steps occur not at once
  - this steps are separated
  - ex. REST API
* Reload method
  -  send essential data and process 1-2-3 Step
* Full Update method
  - send all data and receive
  - remake Entity and update

## Model Change
* Extentions.cs
{% highlight C# %}
  public static IQueryable<GuildDto> MapGuildToDto(this IQueryable<Guild> guild)
  {
    return guild.Select(g => new GuildDto()
    {
      GuildId = g.GuildId,
      ...
      });
    }
  }
{% endhighlight %}

## Reload
* Pros
  - update by minimal data

* Cons
  - read twice

* DbCommands.cs

{% highlight C# %}
  public static void ShowGuilds()
  {
    using(AppDbContext db = new AppDbContext())
    {
      foreach(var guild in db.Guilds.MapGuildToDto().ToList())
      {
        Console.WriteLine($"GuildID({guild.GuildId}) GuildName({guild.Name}) MemberCount({guild.MemberCount})");
      }
    }
  }

  public static void UpdateByReload()
  {
    ShowGuilds();

    // resume to receive DataId/ Data that need to change from out
    Console.WriteLine("Input GuildId");
    Console.Write("> ");
    int id = int.Parse(Console.ReadLine());
    Console.WriteLine("Input GuildNames");
    Console.Write("> ");
    string name = Console.ReadLine();

    using(AppDbContext db = new AppDbContext())
    {
      Guild guild = db.Find<Guild>(id);
      guild.GuildName = name;
      db.SaveChanges();
    }

    Console.WriteLine("----- Update Complete -----");
    ShowGuilds();
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/19.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/19.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Full
* Pros
  - don't neet to read DB again, update directly
  - find data by Primary key, so you don't need to read original database

* Cons
  - need all data
  - Security problem(Confidence in the other party)

* JsonConvert
  - use Json String in C#
  - to make object, use `DeserializeObject` method

* DbCommands.cs by Json
{% highlight C# %}
  // "GuildId":1, "GuildName": "Hello", "Members":null
  public static string MakeUpdateJsonStr()
  {
    var jsonStr = "{\"GuildId\":1, \"GuildName\":Hello, \"Members\":null}";
    return jsonStr;
  }

  public static void UpdateByFull()
  {
    ShowGuilds();

    string jsonStr = MakeUpdateJsonStr();
    Guild guild = JsonConvert.DeserializeObject<Guild>(jsonStr);

    using (AppDbContext db = new AppDbContext())
    {
      db.Guilds.Update(guild);
      db.SaveChanges();
    }

    Console.WriteLine("----- Update Complete -----");
    ShowGuilds();
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/20.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/20.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>


* DbCommands.cs by Object
{% highlight C# %}
  public static void UpdateByFull()
  {
    ShowGuilds();

    string jsonStr = MakeUpdateJsonStr();
    // Guild guild = JsonConvert.DeserializeObject<Guild>(jsonStr);

    Guild guild = new Guild()
    {
      GuildId = 1,
      GuildName = "TestGuild"
    };
    ...
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/21.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/21.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Foreign Key and Nullable

## Relationship
* Principal Entity 
  - ex. Player
* Dependent Entity 
  - Foreign Key
  - ex. Item
* Policy about Relationship
  - Dependent Data can exsist without Principal Data<br>
    1) item cannot exsist without player<br>
    2) item can exsist without item

* Nullable 
  - ex. int?
  - FK → int → 1)<br>
    if player is deleted, item that reference the player is deleted by FK
  - FK → int? → 2)<br>
    if player is deleted, item that reference the player is not deleted by FK

* DbCommands.cs

{% highlight C# %}
  public static void ShowItem()
  {
    using (AppDbContext db = new AppDbContext())
    {
      foreach (var item in db.Items.Include(i => i.Owner).IgnoreQueryFilters().ToList())
      {
        if (item.Owner == null)
          Console.WriteLine($"ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner(0)");
        else
          Console.WriteLine($"ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner({item.Owner})");
      }
    }
  }

  public static void Test()
  {
    ShowItem();

    Console.WriteLine("Input Delete PlayerId");
    Console.Write("> ");
    int id = int.Parse(Console.ReadLine());

    using (AppDbContext db = new AppDbContext())
    {
      Player player = db.Players
                        .Include(p => p.Item)
                        .Single(p => p.PlayerId == id);

      db.Players.Remove(player);
      db.SaveChanges();
    }

    Console.WriteLine("----- Test Complete -----");
    ShowItem();
  }
{% endhighlight %}


## 1) Not Nullable

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    public int OwnerId { get; set; }
    ...  
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/22.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/22.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## 2) Nullable

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    public int? OwnerId { get; set; }
    ...  
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/23.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/23.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/24.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/24.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Update Relationship

## Update Relationship 1:1

* Create new object and remove original data

{% highlight C# %}
  public static void Update_1v1()
  {
    ShowItem();

    Console.WriteLine("Input ItemSwitch PlayerId");
    Console.Write("> ");
    int id = int.Parse(Console.ReadLine());

    using(AppDbContext db = new AppDbContext())
    {
      Player player = db.Players
                        .Include(p => p.Item)
                        .Single(p => p.PlayerId == id);

      player.Item = new Item()
      {
        TemplateId = 777,
        CreateDate = DateTime.Now
      };
      
      db.SaveChanges();
    }
    
    Console.WriteLine("----- Test Complete -----");
    ShowItem();
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/25.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/25.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* Not create new object, just change the data

{% highlight C# %}
  ...
    using(AppDbContext db = new AppDbContext())
    {
      Player player = db.Players
                        .Include(p => p.Item)
                        .Single(p => p.PlayerId == id);

      if (player.Item != null)
      {
        player.Item.TemplateId = 888;
        player.Item.CreateDate = DateTime.Now;
      }
  ...
{% endhighlight %}
  
<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/26.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/26.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Update Realtionship 1vN

* Create new object and remove original data

{% highlight C# %}
  public static void ShowGuild()
  {
    using (AppDbContext db = new AppDbContext())
    {
      foreach (var guild in db.Guilds.Include(g => g.Members).ToList())
      {
        Console.WriteLine($"GuildId({guild.GuildId}) GuildName({guild.GuildName}) MemberCount({guild.Members.Count})");
      }
    }
  }
  
  public static void Update_1vN()
  {
    ShowGuild();

    Console.WriteLine("Input GuildId");
    Console.Write("> ");
    int id = int.Parse(Console.ReadLine());

    using (AppDbContext db = new AppDbContext())
    {
      Guild guild = db.Guilds
                      .Include(g => g.Members)
                      .Single(g => g.GuildId == id);

      guild.Members = new List<Player>()
      {
        new Player() {Name = "Dopa" }
      };

      db.SaveChanges();
    }

    Console.WriteLine("----- Test Complete -----");
    ShowGuild();
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/27.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/27.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/28.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/28.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* Not create new object, just change the data

{% highlight C# %}
  ...
    using (AppDbContext db = new AppDbContext())
    {
      Guild guild = db.Guilds
                      .Include(g => g.Members)
                      .Single(g => g.GuildId == id);
      
      /*
      guild.Members = new List<Player>()
      {
        new Player() {Name = "Dopa" }
      };
      */

      guild.Members.Add(new Player() { Name = "Dopa" });
      guild.Members.Add(new Player() { Name = "Keria" });
      guild.Members.Add(new Player() { Name = "Pyosik" });

      db.SaveChanges();
    }
...
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/29.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/29.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/30.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/30.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Delete

## Model Change
* DataModel.cs

{% highlgiht C# %}
  [Table("Item")]
  public class Item
  {
    ...
    public bool SoftDeleted { get; set; }
    ...
  }
{% endhighlight %}

* AppDbContext.cs
  - filtering of model level when accessing Item entity 
  - if want ignore filter, add IgnoreQueryfilters option
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    builder.Entity<Item>().HasQueryFilter(i => i.SoftDeleted == false);
  }
{% endhighlight %}

## SoftDelete

* Delete
  - Tracking Entity
  - call Remove
  - call SaveChanges

* SoftDelte
  - not remove the data in DB
  - just check that the data is removed

* DbCommands.cs

{% highlight C# %}
  public static void ShowItem()
  {
    using (AppDbContext db = new AppDbContext())
    {
      foreach (var item in db.Items.Include(i => i.Owner).IgnoreQueryFilters().ToList())
      {
        if (item.SoftDeleted)
        {
          Console.WriteLine($"DELETED - ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner(0)");
        }
        else
        {                        
          if (item.Owner == null)
            Console.WriteLine($"ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner(0)");
          else
            Console.WriteLine($"ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner({item.Owner.Name})");
        }
      }
    }
  }

  public static void TestDelete()
  {
    ShowItem();
    Console.WriteLine("Select Delete ItemId");
    Console.Write("> ");
    int id = int.Parse(Console.ReadLine());

    using (AppDbContext db = new AppDbContext())
    {
      Item item = db.Items.Find(id);
      
      item.SoftDeleted = true;
      db.SaveChanges();
    }

    Console.WriteLine("----- Test Complete -----");
    ShowItem();
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/31.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/31.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/32.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/32.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>


# Configuration

## Convention
* Convention
  - make form and name with fixed rule, be processed automately in EF Core
  - easy and fast, but not include all case

* Entity Class
  - public access modifier + non-static
  - search and analize public getter during property
  - property name = table column name

* name, structure, size
  - .NET structure ↔ SQL structure<br>
    ex. int, bool
  - follow Nullable or not of .NET structure<br>
    ex. string is nullable<br>
    ex. int non-nul vs int? is nullable

* PK
  - define property which name is Id or [ClassName]Id to PK
  - Composite Key cannot be defined by Convention

## Data Annotation
  - add Attribute at class/property
## Fluent Api
  - set Setting directly in OnModelCreating
  - is the biggest in application range

* DB Column type, size, nullable

<table>
  <thead>
    <tr>
      <th></th>
      <th>Data Annotation</th>
      <th>Fluent Api</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Nullable</th>
      <td>[Required]</td>
      <td>.Isrequired</td>
    </tr>
    <tr>
      <th>String length</th>
      <td>[MayLength(20)]</td>
      <td>.Has MaxLength(20)</td>
    </tr>
    <tr>
      <th>char form</th>
      <td></td>
      <td>.IsUnicode(true)</td>
    </tr>
  </tbody>
</table>
<p>
  - Nullable         [Required]              .IsRequired()
  - String length    [MayLength(20)]         .Has MaxLength(20)
  - char form                                .IsUnicode(true)
</p>

* PK
<p>
  - Primary Key      [Key][Column(Order=0)]  .HasKey(x => new {x.Prop1, x.Prop2})<br>
                     [Key][Column(Order=1)]
</p>

* Index
<p>
  - add Index                                .HasIndex(p => p.Prop1)
  - add complex index                        .HasIndex(p => new {p.Prop1, p.Prop2})
  - set index name                           .HasIndex(p => p.Prop1).HasName("Index_MyProp")
  - add unique Index                         .HasIndex(p => p.Prop1).IsUnique()
</p>

* table name
<p>
  - DbSet<T>          [Property/Class]name
                      [Table("MyTable")]      .ToTable("MyTable")
</p>

* column name
  - in Convention, using [Property]name
<p>
  -                   [Column("MyCol")]       .HasColumnName("MyCol")
</p>

* use code modeling, but not in DB modeling(Property and Class)
<p>
  -                   [NotMapped]             .Ignore()
</p>

* Soft Delete
<p>
  -                                           .HasQueryFilter()
</p>

* Order
  - Convention is the easist
  - Validation and related parts → Data Annotation(directly, call SaveChanges)
  - etc → Fluent Api
