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

{% highlight C# %}
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

* PK

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
      <th>Primary Key</th>
      <td>[Key][Column(Order=0)]<br>
          [Key][Column(Order=1)]</td>
      <td>.HasKey(x => new {x.Prop1, x.Prop2})</td>
    </tr>
  </tbody>
</table>

* Index

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
      <th>add Index</th>
      <td></td>
      <td>.HasIndex(p => p.Prop1)</td>
    </tr>
    <tr>
      <th>add complex index</th>
      <td></td>
      <td>.HasIndex(p => new {p.Prop1, p.Prop2})</td>
    </tr>
    <tr>
      <th>set index name</th>
      <td></td>
      <td>.HasIndex(p => p.Prop1).HasName("Index_MyProp")</td>
    </tr>
    <tr>
      <th>add unique Index</th>
      <td></td>
      <td>.HasIndex(p => p.Prop1).IsUnique()</td>
    </tr>
  </tbody>
</table>

* table name

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
      <th>DbSet &lt;T &gt;</th>
      <td>
        [Property/Class]name<br>
        [Table("MyTable")]</td>
      <td>.ToTable("MyTable")</td>
    </tr>
  </tbody>
</table>

* column name
  - in Convention, using [Property]name

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
      <th>Column Name&lt;T&gt;</th>
      <td>[Column("MyCol")]</td>
      <td>.HasColumnName("MyCol")</td>
    </tr>
  </tbody>
</table>

* use code modeling, but not in DB modeling(Property and Class)

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
      <th>use only in code&lt;T&gt;</th>
      <td>[NotMapped]</td>
      <td>.Ignore()</td>
    </tr>
  </tbody>
</table>

* Soft Delete

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
      <th>Soft Delete&lt;T&gt;</th>
      <td></td>
      <td>.HasQueryFilter()</td>
    </tr>
  </tbody>
</table>

* Order
  - Convention is the easist
  - Validation and related parts → Data Annotation(directly, call SaveChanges)
  - etc → Fluent Api

### Test

* DataModel.cs
{% highlight C# %}
  [Table("Player")]
  public class Player
  {
    ...
    [Required]
    [MaxLength(20)]
    public string Name { get; set; }
    ...
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    builder.Entity<Player>()
           .HasIndex(p => p.Name)
           .HasName("Index_Person_Name")
           .IsUnique();
    ...
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/33.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/33.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/34.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/34.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Relationship Configuration

* Relationship
  - Principal Entity
  - Dependent Entity
  - Navigational Property
  - Primary Key(PK)
  - Foreign Key(FK)
  - Principal Key = PK or Unique Alternate Key
  - Required Relationship(Not-Null)
  - Optional Relationship(Nullable)

* Set FK by Convention
  - [PrincipalKeyName]<br>
    ex. PlayerId
  - [Class][PrincipalKeyName]<br>
    ex. PlayerPlayerId
  - [NagivationalPropertyName][PrincipalKeyName]<br>
    ex. OwnerPlayerId / OwnerId

## FK and Nullable

* Required Relationship(Not-Null)
  - when delete, call `OnDelete` factor by `Cascase` mode → if delete Principal, delete Dependent

* Optional Relationship(Nullable)
  - when delete, call `OnDelete` factor by `ClientSetNull` mode
  - if delete Principal and Dependent Tracking, FK is setted null
  - if delete Principal and not Dependent Tracking, occur Exception

## Not in Convention
* Complex FK
* several Navigational Property reference same class
* DB or Delete Customizing

## Set Relationship with Data Annotation
* [ForeignKey("Prop1")]
* [InversProperty] 
  - several Navigational Property reference same class

## Set Relationship with Fluent Api

<table>
  <thead>
  </thead>
  <tbody>
    <tr>
      <td>.HasOne()</td>
      <td>.HasMany()</td>
    </tr>
    <tr>
      <td>.WithOne()</td>
      <td>.WithMany()</td>
    </tr>
    <tr>
      <td>.HasForeignKey()</td>
      <td>.IsRequired()</td>
      <td>.OnDelete()</td>
    </tr>
  </tbody>
</table>

### Test

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    public int TestOwnerId { get; set; }
    // public int OwnerId { get; set; }
    ...
    public int? TestCreatorId { get; set; }
    public Player Creator { get; set; }
    ...
  }

  [Table("Player")]
  public class Player
  {
    ...
    public Item OwnedItem { get; set; }
    public ICollection<Item> CreatedItems { get; set; }
    //public Item Item { get; set; }
    ...
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    builder.Entity<Player>()
           .HasMany(p => p.CreatedItems)
           .WithOne(i => i.Creator)
           .HasForeignKey(i => i.TestCreatorId);

    builder.Entity<Player>()
           .HasOne(p => p.OwnedItem)
           .WithOne(i => i.Owner)
           .HasForeignKey<Item>(i => i.TestOwnerId);
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/35.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/35.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/36.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/36.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Shadow Property & Backing Field

## Shadow Property
  - in Class OK, but Not in DB

<table>
  <thead>
    <tr>
      <th></th>
      <th>Data Annotation</th>
      <th>Fluent Api</th>
    </tr>
  </thead>
    <tr>
      <th>only use in code</th>
      <td>[NotMapped]</td>
      <td>.Ignore()</td>
    </tr>
  <tbody>
  </tbody>
</table>

  - in DB OK, but Not in Class → Shadow Property

<table>
  <thead>
    <tr>
      <th></th>
      <th>Data Annotation</th>
      <th>Fluent Api</th>
    </tr>
  </thead>
    <tr>
      <th>create</th>
      <td></td>
      <td>.Property&lt;DateTime&gt;("RecoveredDate")</td>
    </tr>
    <tr>
      <th>Read/Write</th>
      <td></td>
      <td>.Property("RecoveredDate").CurrentVale</td>
    </tr>
  <tbody>
  </tbody>
</table>

### Test

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    builder.Entity<Item>().Property<DateTime>("RecoveredDate");
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    db.Entry(items[0]).Property("RecoveredDate").CurrentValue = DateTime.Now;
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/37.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/37.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/38.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/38.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Backing Field (EF Core)
  - mapping private field in DB and change to public getter to use
  - ex. save string by Json type in DB and getter use json with processing
  - Fluent Api in normal

* DataModel.cs
{% highlight C# %}
  public struct ItemOption
  {
    public int str;
    public int dex;
    public int hp;
  }

  [Table("Item")]
  public class Item
  {
    private string _jsonData;
    public string JsonData
    {
      get { return _jsonData; }
      //set { _jsonData = value; }
    }

    public void SetOption(ItemOption option)
    {
      _jsonData = JsonConvert.SerializeObject(option);
    }

    public ItemOption GetOption()
    {
      return JsonConvert.DeserializeObject<ItemOption>(_jsonData);
    }
    ...
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    builder.Entity<Item>()
                .Property(i => i.JsonData)
                .HasField("_jsonData");
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/39.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/39.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/40.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/40.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/41.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/41.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Entity ↔ DB Table link
  - all Entity Class Read/ Write → Cost (Select Loading, DTO)

# Owned Type
  - add normal Class in Entity Class

## add to same table
* .OwnsOne()
  - Relationship .Include() vs Ownership

### Test

* DataModel.cs
{% highlight C# %}
  //public struct ItemOption
  //{
  //    public int str;
  //    public int dex;
  //    public int hp;
  //}

  public class ItemOption
  {
    public int Str { get; set; }
    public int Dex { get; set; }
    public int Hp { get; set; }
  }

  [Table("Item")]
  public class Item
  {
    //private string _jsonData;
    //public string JsonData
    //{
    //  get { return _jsonData; }
    //  set { _jsonData = value; }
    //}

    //public void SetOption(ItemOption option)
    //{
    //  _jsonData = JsonConvert.SerializeObject(option);
    //}

    //public ItemOption GetOption()
    //{
    //  return JsonConvert.DeserializeObject<ItemOption>(_jsonData);
    //}
    
    public ItemOption Option { get; set; }
  }
{% endhighlight %}


* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    builder.Entity<Item>()
           .OwnsOne(i => i.Option);
    ...
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    items[1].Option = new ItemOption() { Dex = 1, Hp = 2, Str = 3 };
    ...
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/42.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/42.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/43.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/43.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/44.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/44.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## add to diff table
* .OwnsOne().ToTable()

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    //builder.Entity<Item>()
    //       .OwnsOne(i => i.Option);

    builder.Entity<Item>()
           .OwnsOne(i => i.Option)
           .ToTable("ItemOption");
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void ShowItem()
  {
    using (AppDbContext db = new AppDbContext())
    {
      foreach (var item in db.Items.Include(i => i.Owner).IgnoreQueryFilters().ToList())
      {
        ...
        else
        {
          if (item.Option != null)
            Console.WriteLine("STR " + item.Option.Str);
          ...
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/45.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/45.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/46.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/46.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/47.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/47.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Table Per Hiearchy (TPH)
  - several classes of hierarchy relation ↔ mapping in one table<br>
    ex. Dog, Cat, Bird, Animal

## Convention
  - first make class by hiearchy and add DbSet
  - Discriminator

### Test

* DataModel.cs
{% highlight C# %}
  public class EventItem : Item
  {
    public DateTime DestroyDate { get; set; }
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  class AppDbContext : DbContext
  {
    public DbSet<EventItem> EventItems { get; set; }
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/48.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/48.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/49.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/49.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/50.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/50.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Fluent Api
  - .HasDiscriminator().HasValue()

### Test

* DataModel.cs
{% highlight C# %}
  public enum ItemType
  {
    NormalItem,
    EventItem
  }
  
  [Table("Item")]
  public class Item
  {
    public ItemType Type { get; set; }
    ...
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  class AppDbContext : DbContext
  {
    //public DbSet<EventItem> EventItems { get; set; }
  }

  protected override void OnModelCreating(ModelBuilder builder)
  { 
    ...           
    builder.Entity<Item>()
           .HasDiscriminator(i => i.Type)
           .HasValue<Item>(ItemType.NormalItem)
           .HasValue<EventItem>(ItemType.EventItem);
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    new EventItem()
    {
      ...
      DestroyDate =  DateTime.Now
    },
    ...
  }

  public static void ShowItem()
  {
    using (AppDbContext db = new AppDbContext())
    {
      foreach (var item in db.Items.Include(i => i.Owner).IgnoreQueryFilters().ToList())
      {
        ...
        else
        {
          ...
          EventItem eventItem = item as EventItem;
          if (eventItem != null)
            Console.WriteLine("DestroyDate: " + eventItem.DestroyDate);
          ...
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/51.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/51.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/52.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/52.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/53.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/53.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Table Splitting
  - several entity Class ↔ mapping one table

### Test
* DataModel.cs
{% highlight C# %}
      public class ItemDetail
    {
        public int ItemDetailId { get; set; }
        public string Description { get; set; }
    }

    [Table("Item")]
    public class Item
    {
      ...
      public ItemDetail Detail { get; set; }
      ...
    }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    //builder.Entity<Item>()
    //       .HasDiscriminator(i => i.Type)
    //       .HasValue<Item>(ItemType.NormalItem)
    //       .HasValue<EventItem>(ItemType.EventItem);

    builder.Entity<Item>()
           .HasOne(i => i.Detail)
           .WithOne()
           .HasForeignKey<ItemDetail>(i => i.ItemDetailId);

    builder.Entity<Item>().ToTable("Items");
    builder.Entity<ItemDetail>().ToTable("Items");
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    items[2].Detail = new ItemDetail()
    {
      Description = "This is a good item."
    };
  }

  public static void ShowItem()
  {
    using (AppDbContext db = new AppDbContext())
    {
      //foreach (var item in db.Items.Include(i => i.Owner).IgnoreQueryFilters().ToList())
      foreach (var item in db.Items.Include(i => i.Owner).Include(i => i.Detail).IgnoreQueryFilters().ToList())
      {
        ...
        else
        {
          ...
          if (item.Detail != null)
            Console.WriteLine(item.Detail.Description);
  }
{% endhighlight %}


<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/54.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/54.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/55.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/55.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/56.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/56.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Backing Field & Relationship

## Backing Field → mapping private field in DB
  - can use in Navigation Property

### Test

* DataModel.cs
{% highlight C# %}
  //public class ItemOption
  //{
  //  public int Str { get; set; }
  //  public int Dex { get; set; }
  //  public int Hp { get; set; }
  //}

  //public class ItemDetail
  //{
  //  public int ItemDetailId { get; set; }
  //  public string Description { get; set; }
  //}

  //public enum ItemType
  //{
  //  NormalItem,
  //  EventItem
  //}

  public class ItemReview
  {
    public int ItemReviewId { get; set; }
    public int Score { get; set; } // 0~5
  }

  [Table("Item")]
  public class Item
  {
    //public ItemType Type { get; set; }
    //public ItemOption Option { get; set; }
    //public ItemDetail Detail { get; set; }
    ...
    //public int? TestCreatorId { get; set; }
    //public Player Creator { get; set; }

    public double? AverageScore { get; set; }

    private readonly List<ItemReview> _reviews = new List<ItemReview>();
    public IEnumerable<ItemReview> Reviews { get { return _reviews.ToList(); } }

    public void AddReview(ItemReview review)
    {
      _reviews.Add(review);
      AverageScore = _reviews.Any() ? _reviews.Average(r => r.Score) : (double?)null;
    }

    public void RemoveReview(ItemReview review)
    {
      _reviews.Remove(review);
    }
  }

  //public class EventItem : Item
  //{
  //  public DateTime DestroyDate { get; set; }
  //}
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  class AppDbContext : DbContext
  {
    ...
    //public DbSet<EventItem> EventItems { get; set; }
    ...
  }

  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    //builder.Entity<Player>()
    //       .HasMany(p => p.CreatedItems)
    //       .WithOne(i => i.Creator)
    //       .HasForeignKey(i => i.TestCreatorId);
    ...
    //builder.Entity<Item>()
    //       .OwnsOne(i => i.Option)
    //       .ToTable("ItemOption");
    ...
    //builder.Entity<Item>()
    //       .HasOne(i => i.Detail)
    //       .WithOne()
    //       .HasForeignKey<ItemDetail>(i => i.ItemDetailId);

    //builder.Entity<Item>().ToTable("Items");
    //builder.Entity<ItemDetail>().ToTable("Items");

    builder.Entity<Item>()
           .Metadata
           .FindNavigation("Reviews")
           .SetPropertyAccessMode(PropertyAccessMode.Field);
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    //new EventItem()
    new Item()
    {
      ...
      //DestroyDate =  DateTime.Now
    },
    //items[1].Option = new ItemOption() { Dex = 1, Hp = 2, Str = 3 };

    //items[2].Detail = new ItemDetail()
    //{
    //    Description = "This is a good item."
    //};

    items[0].AddReview(new ItemReview() { Score = 5 });
    items[0].AddReview(new ItemReview() { Score = 4 });
    items[0].AddReview(new ItemReview() { Score = 1 });
    items[0].AddReview(new ItemReview() { Score = 5 });

    ...
    //{
    //  var owner = db.Players.Where(p => p.Name == "Hanna").First();

    //  Item item = new Item()
    //  {
    //    TemplateId = 300,
    //    CreateDate = DateTime.Now,
    //    Owner = owner
    //  };
    //  db.Items.Add(item);
    //  db.SaveChanges();
    //}
  }

  public static void ShowItem()
  {
    using (AppDbContext db = new AppDbContext())
    {
      //foreach (var item in db.Items.Include(i => i.Owner).Include(i => i.Detail).IgnoreQueryFilters().ToList())
      foreach (var item in db.Items.Include(i => i.Owner).IgnoreQueryFilters().ToList())
      {
        ...
        else
        {
          //if (item.Option != null)
          //  Console.WriteLine("STR " + item.Option.Str);

          //EventItem eventItem = item as EventItem;
          //if (eventItem != null)
          //  Console.WriteLine("DestroyDate: " + eventItem.DestroyDate);

          //if (item.Detail != null)
          //  Console.WriteLine(item.Detail.Description);

          if(item.AverageScore == null)
            Console.WriteLine("Score(None)");
          else
            Console.WriteLine($"Score({item.AverageScore})");

          if (item.Owner == null)
            Console.WriteLine($"ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner(0)");
          else
            Console.WriteLine($"ItemId({item.ItemId}) TemplateId({item.TemplateId}) Owner({item.Owner.Name})");
        }
      }
    }
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/57.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/57.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/58.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/58.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/59.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/59.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# UserDefined Function(UDF)
  - The ability to invoke SQL that you create
  - want to perform operations on the DB side.
  - EF Core queries are inefficient

## Steps
* Configuration
  - make static function and assign EF Core
* Database Setup
* Use

### Test

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    //public double? AverageScore { get; set; }

    //private readonly List<ItemReview> _reviews = new List<ItemReview>();
    //public IEnumerable<ItemReview> Reviews { get { return _reviews.ToList(); } }

    //public void AddReview(ItemReview review)
    //{
    //  _reviews.Add(review);
    //  AverageScore = _reviews.Any() ? _reviews.Average(r => r.Score) : (double?)null;
    //}

    //public void RemoveReview(ItemReview review)
    //{
    //  _reviews.Remove(review);
    //}

    public ICollection<ItemReview> Reviews { get; set; }
  }
{% endhighlight %}

* Program.cs
{% highlight C# %}
  // assign EFCore
  // Annotation(Attribute)
  [DbFunction()]
  public static double? GetAverageReviewScore(int ItemId)
  {
    throw new NotImplementedException("Don't Use!");
  }
{% endhighlight %}


* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    //builder.Entity<Item>()
    //       .Metadata
    //       .FindNavigation("Reviews")
    //       .SetPropertyAccessMode(PropertyAccessMode.Field);

    // DbFunction
    builder.HasDbFunction(() => Program.GetAverageReviewScore(0));
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void InitializeDB(bool forceReset = false)
  {
    using (AppDbContext db = new AppDbContext())
    {
      ...
      string command = 
        @" CREATE FUNCTION GetAverageReviewScore (@itemId INT) RETURNS FLOAT
           AS
           BEGIN

           DECLARE @result AS FLOAT

           SELECT @result = AVG(CAST([Score] AS FLOAT))
           FROM ItemReview AS r
           WHERE @itemId = r.ItemId

           RETURN @result

           END";

      db.Database.ExecuteSqlRaw(command);
      ...
    }
  }

  public static void CreateTestData(AppDbContext db)
  {
    ...
    //items[0].AddReview(new ItemReview() { Score = 5 });
    //items[0].AddReview(new ItemReview() { Score = 4 });
    //items[0].AddReview(new ItemReview() { Score = 1 });
    //items[0].AddReview(new ItemReview() { Score = 5 });

    items[0].Reviews = new List<ItemReview>()
    {
      new ItemReview(){Score = 5},
      new ItemReview(){Score = 3},
      new ItemReview(){Score = 2},
    };
            
    items[1].Reviews = new List<ItemReview>()
    {
      new ItemReview(){Score = 1},
      new ItemReview(){Score = 1},
      new ItemReview(){Score = 0},
    };
    ...
  }

  public static void ShowItem(AppDbContext db)
  {
    ...
    //if(item.AverageScore == null)
    //  Console.WriteLine("Score(None)");
    //else
    //  Console.WriteLine($"Score({item.AverageScore})");
  }

  public static void CalcAverage()
  {
    using(AppDbContext db = new AppDbContext())
    {
      foreach(double? average in db.Items.Select(i => Program.GetAverageReviewScore(i.ItemId)))
      {
        if(average == null)
          Console.WriteLine("No Review!");
        else
          Console.WriteLine($"Average: {average.Value}");
      }
    }
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/60.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/60.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/61.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/61.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/62.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/62.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Default Value

## How to be defined
* defined by Entity Class
* defined by DB Table
  - if you use DB by other way except EF ↔ DB, it makes diff<br>
    ex) SQL Script 

## Auto-Property Initializer(C# 6.0)
* Entity default value
  - apply DB by SaveChange

### Test

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    public DateTime CreateDate { get; set; } = new DateTime(2020, 1, 1);
    ...
    //public ICollection<ItemReview> Reviews { get; set; }
  }

  //public class ItemReview
  //{
  //    public int ItemReviewId { get; set; }
  //    public int Score { get; set; } // 0~5
  //}
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}

  public static CreateTestData(AppDbContext db)
  {
    ...
    //items[0].Reviews = new List<ItemReview>()
    //{
    //  new ItemReview(){Score = 5},
    //  new ItemReview(){Score = 3},
    //  new ItemReview(){Score = 2},
    //};
            
    //items[1].Reviews = new List<ItemReview>()
    //{
    //  new ItemReview(){Score = 1},
    //  new ItemReview(){Score = 1},
    //  new ItemReview(){Score = 0},
    //};
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/63.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/63.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/64.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/64.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/65.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/65.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Fluent Api
* DB Table
  - DEFAULT

### Test
* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    //builder.HasDbFunction(() => Program.GetAverageReviewScore(0));

    builder.Entity<Item>()
           .Property("CreateDate")
           .HasDefaultValue(DateTime.Now);
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/66.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/66.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/67.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/67.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/68.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/68.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## SQL Fragment 
* .HasDefaultValueSql
  - when add new value, excute in DB

### Test

* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    //public DateTime CreateDate { get; set; } = new DateTime(2020, 1, 1);
    public DateTime CreateDate { get; private set; }
    ...
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...

    //builder.Entity<Item>()
    //       .Property("CreateDate")
    //       .HasDefaultValue(DateTime.Now);

    builder.Entity<Item>()
           .Property("CreateDate")
           .HasDefaultValueSql("GETDATE()");
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    List<Item> items = new List<Item>()
    {
      new Item()
      {
        TemplateId = 101,
        //CreateDate = DateTime.Now,
        Owner = hanna
      },
      new Item()
      {
        TemplateId = 102,
        //CreateDate = DateTime.Now,
        Owner = faker,
      },
      new Item()
      {
        TemplateId = 103,
        //CreateDate = DateTime.Now,
        Owner = deft
      }
    };
    ...
  }

  //public static void UpdateDate()
  //{
  //  Console.WriteLine("Input Player Name");
  //  Console.Write("> ");
  //  string name = Console.ReadLine();

  //  using(var db = new AppDbContext())
  //  {
  //    var items = db.Items.Include(i => i.Owner)
  //                  .Where(i => i.Owner.Name == name);

  //    foreach(Item item in items)
  //    {
  //      item.CreateDate = DateTime.Now;
  //    }

  //    db.SaveChanges();
  //  }

  //  ReadAll();
  //}
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/69.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/69.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/70.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/70.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/71.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/71.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Value Generator
  - excute in EF Core
  - set Generator rule

### Test

* DataModel.cs
{% highlight C# %}
  public class PlayerNameGenerator : ValueGenerator<string>
  {
    public override bool GeneratesTemporaryValues => false;

    public override string Next(EntityEntry entry) 
    {
      string name = $"Player_{DateTime.Now.ToString("yyyyMMdd")}";
      return name;
    }
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  protected override void OnModelCreating(ModelBuilder builder)
  {
    ...
    builder.Entity<Player>()
           .Property(p => p.Name)
           .HasValueGenerator((p, e) => new PlayerNameGenerator());
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    //var hanna = new Player() { Name = "Hanna" };
    var hanna = new Player() { };
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/72.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/72.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/73.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/73.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>


# Migration
  - EF Core DbContext ↔ need agreement about DB state

## Code-First
  - the standard is Entity Class or Db Context
  - not always update recently

* Migration Step<br>
  1) make Migration<br> 
  2) apply Migration<br>

* Add-Migration [Name]<br>
  1) search DbContext and analize → DB Modeling(recent)<br>
  2) DB Modeling most latest Migration state by ModelSnapshot.cs<br>
  3) 1-2 Compare and print result<br>
    &nbsp; a) ModelSnapshot<br>
    &nbsp; b) Migrate.Designer.cs and Migrate.cs -> detail data related with Migration <br>
    - Add Up/Down by Hands

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/74.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/74.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/75.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/75.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/76.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/76.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* apply Migration<br>
  1) SQL change script<br>
    - Script-Migration [From] [To] [Options]<br>
  2) call Database.Migrate <br>
  3) Command Line method<br>
    - Update-Database [options]

### Test
* DataModel.cs
{% highlight C# %}
  [Table("Item")]
  public class Item
  {
    ...
    public int ItemGreade { get; set; }
    ...
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/77.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/77.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/78.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/78.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* Sync to specific Migration 
  - Update-Database [Name]

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/79.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/79.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* Delete last Migration
  - Remove-Migration

<figure>
  <a href="/assets/img/posts/efcore_mmo_efcore/80.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/80.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## Database-First
  - if you have Database, you can make EF Core DbContext from Database

* Download
  - <a href="https://marketplace.visualstudio.com/items?itemName=ErikEJ.EFCorePowerTools">EFCore Poser Tools</a>

* Reverse Engineer
  - [EF Core Power Tools] - [Reverse Engineer]
<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/81.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/81.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/82.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/82.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/83.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/83.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>
<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/84.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/84.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/85.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/85.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/86.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/86.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

## SQL-First
* Methods
  - Hands Power
  - Script-Migration [From] [To] [Options]
  - Compare DB and extract SQL

# DbContext(Optimize)

## ChangeTracker
* Tracking State
  - State is mostly intuitive but when it include Relationship, it becames more complexive

## Database
* Transaction
* DB Creation / Migration
* Raw SQL

## Model
* DB Modeling

* State Check method
  - Entry().State
  - Entry().Property().IsModified
  - Entry().Navigation().IsModified

* state changing on Add/AddRange
  - NotTracking State → Added
  - Tracking State → seperated Modified or current state maintain by FK setting 

* state changing on Remove/RemoveRange
  - (key that made by DB) && (not C# default value) → Updated, Modified or Deleted
  - (not Key that made by DB) || (C# default value) → Added
  - above is Added because of behavior consistency
  - DB need to the existence first (ex. Cascade Delete Process)

### Test

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    var hanna = new Player() { Name = "Hanna" };
    //var hanna = new Player() { };
    ...
    
    db.SaveChanges();

    // Add Test
    {
      Item item = new Item()
      {
        TemplateId = 500,
        Owner = hanna
      };
      db.Items.Add(item);
      // add item -> indirectly infect Player
      // Player is Tracking state, it doesn't need to FK setting 

      Console.WriteLine("2.)" + db.Entry(hanna).State);
    }

    // Delete Test
    {
      Player p = db.Players.First();

      // DB don't know the new guild (not exist DB key)
      p.Guild = new Guild() { GuildName = "This will be deleted Soon" };
      // Item is in DB already (exist DB Key)
      p.OwnedItem = items[0];

      db.Players.Remove(p);

      // remove Player directly
      Console.WriteLine("3.)" + db.Entry(p).State); // Deleted
      Console.WriteLine("4.)" + db.Entry(p.Guild).State); // Added
      Console.WriteLine("5.)" + db.Entry(p.OwnedItem).State); // Deleted
    }
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/87.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/87.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/88.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/88.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* Update/UpdateRange
  - the normal method to update Entity in EF is not Update
  - Tracked Entity → change property → SaveChanges
  - Update is to update all Untracked Entity (Disconnected State)

* Update Step in EF Core<br>
  1) call Update<br>
  2) Entity State = Modified<br>
  3) all IsModified of Non-Relational Property = true

* Relationship in Update
  - (key that made by DB) && (not 0 → Updated, Modified or Deleted
  - (not Key that made by DB) || (0) → Added

* Attach
  - change Untracked Entity to Tracked Entity

* Relationship in Attach
  - (key that made by DB) && (not 0 → Unchanged
  - (not Key that made by DB) || (0) → Added

### Test

* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    List<Item> items = new List<Item>()
    {
      ...
      //new Item()
      //{
      //  TemplateId = 102,
      //  Owner = faker,
      //},
      //new Item()
      //{
      //  TemplateId = 103,
      //  Owner = deft
      //}
    };

    //db.Entry(items[2]).Property("RecoveredDate").CurrentValue = DateTime.Now;
    ...
  }

  public static void TestUpdateAttach()
  {
    using (AppDbContext db = new AppDbContext())
    {
      // Update Test
      {
        // Disconnected
        Player p = new Player();
        p.PlayerId = 2;
        p.Name = "FakerLegend";

        // DB don't know the new guild(not exist DB Key)
        p.Guild = new Guild() { GuildName = "Update Guild" };

        Console.WriteLine("6) " + db.Entry(p.Guild).State); // Detached
        db.Players.Update(p);
        Console.WriteLine("7) " + db.Entry(p.Guild).State); // Added
      }

      //Attach Test
      {
        Player p = new Player();

        // TEMP
        p.PlayerId = 3;
        p.Name = "DeftHero";
        p.Guild = new Guild() { GuildName = "Update Guild" };

        Console.WriteLine("8) "+ db.Entry(p.Guild).State); // Detached
        db.Players.Attach(p);
        Console.WriteLine("9) " + db.Entry(p.Guild).State); // Added
      }
    }
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/89.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/89.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/90.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/90.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

* DbCommands.cs
{% highlight C# %}
  public static void TestUpdateAttach()
  {
    ...
    //Attach Test
    {
      Player p = new Player();

      // TEMP
      p.PlayerId = 3;
      //p.Name = "DeftHero";
      p.Guild = new Guild() { GuildName = "Update Guild" };

      Console.WriteLine("8) "+ db.Entry(p.Guild).State); // Detached
      db.Players.Attach(p);
      p.Name = "DeftHero";
      Console.WriteLine("9) " + db.Entry(p.Guild).State); // Added
    }
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/91.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/91.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/92.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/92.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# State Control
  - can control State (ex. Optimization)
  - Entry().State = EntityState.Added
  - Entry().Property("").IsModified = true

## TrackGraph
  - State Control of Untracked Entity in Relationship<br>
  ex. change speficific data

## ChangeTracker
  - detect state data changing<br>
  ex. Print log when name of player changing<br>
  ex. Put Validation code<br>
  ex. add CreateTime data when player is created time

* Steps<br>
  1) override SaveChanges<br>
  2) extract / use changing data by ChangeTracker.Entries

### Test
* DataModel.cs
{% highlight C# %}
  // detect Created Time
  public interface ILogEntity
  {
    DateTime CreateTime { get; }
    void SetCreateTime();
  }

  [Table("Player")]
  public class Player
  {
    ...
    //public ICollection<Item> CreatedItems { get; set; }
    ...
    public DateTime CreateTime { get; private set; }

    public void SetCreateTime()
    {
      CreateTime = DateTime.Now;
    }
  }
{% endhighlight %}

* AppDbContext.cs
{% highlight C# %}
  public override int SaveChanges()
  {
    var entities = ChangeTracker.Entries()
                                .Where(e => e.State == EntityState.Added);

    foreach(var entity in entities)
    {
      ILogEntity tracked = entity.Entity as ILogEntity;
      if (tracked != null)
        tracked.SetCreateTime();
    }
    return base.SaveChanges();
  }
{% endhighlight %}

* DbCommands.cs
{% highlight C# %}
  public static void StateControl()
  {
    using (AppDbContext db = new AppDbContext())
    {
      // State Control
      {
        Player p = new Player { Name = "StateTest" };
        db.Entry(p).State = EntityState.Added; // change Tracked
        // db.Players.Add(p);
        db.SaveChanges();
      }

      // TrackGraph
      {
        // Disconnected State
        // not all change, only change player name
        Player p = new Player()
        {
          PlayerId = 2,
          Name = "Faker_New",
        };

        p.OwnedItem = new Item() { TemplateId = 777 }; // item data
        p.Guild = new Guild() { GuildName = "TrackGraphGuild" }; // guild data

        db.ChangeTracker.TrackGraph(p, e =>
        {
          if(e.Entry.Entity is Player)
          {
            e.Entry.State = EntityState.Unchanged;
            e.Entry.Property("Name").IsModified = true;
          }
          else if(e.Entry.Entity is Guild)
          {
            e.Entry.State = EntityState.Unchanged;
          }
          else if(e.Entry.Entity is Item)
          {
            e.Entry.State = EntityState.Unchanged;
          }
        });
        db.SaveChanges();
      }
    }
  }
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/efcore_mmo_efcore/93.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/93.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/94.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/94.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/95.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/95.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# call SQL direct
  - you can call your SQL<br>
  ex. not to LINQ → call Stored Procedure, etc<br>
  ex. Performance Optimizing

## FormSql → FromSql / FormSqlInterpolated
  - Add Raw SQL in EF Core Query

## ExecuteSqlCommand → ExcuteSqlRaw / ExecuteSqlInterpolated
  - Non-Query SQL(not SELECT)

## Reload
  - there is Tracked Entity already
  - when DB data is changed by ExecuteSqlCommand, then Reload

### Test
* DbCommands.cs
{% highlight C# %}
  public static void CreateTestData(AppDbContext db)
  {
    ...
    List<Item> items = new List<Item>()
    {
      new Item()
      {
        TemplateId = 101,
        Owner = hanna
      },
      new Item()
      {
        TemplateId = 102,
        Owner = faker,
      },
      new Item()
      {
        TemplateId = 103,
        Owner = deft
      }
    };
    
    //Console.WriteLine("1.)" + db.Entry(hanna).State);

    //db.SaveChanges();

    //{
    //  Item item = new Item()
    //  {
    //    TemplateId = 500,
    //    Owner = hanna
    //  };
    //  db.Items.Add(item);

    //  Console.WriteLine("2.)" + db.Entry(hanna).State);
    //}

    //{
    //  Player p = db.Players.First();
    //  p.Guild = new Guild() { GuildName = "This will be deleted Soon" };
    //  p.OwnedItem = items[0];

    //  db.Players.Remove(p);

    //  Console.WriteLine("3.)" + db.Entry(p).State); // Deleted
    //  Console.WriteLine("4.)" + db.Entry(p.Guild).State); // Added
    //  Console.WriteLine("5.)" + db.Entry(p.OwnedItem).State); // Deleted
    //}
  }

  public static void CallSQL()
  {
    using(AppDbContext db = new AppDbContext())
    {
      // FormSql
      {
        string name = "Hanna";

        // SQL Injection(Web Hacking)

        var list = db.Players
                     .FromSqlRaw("SELECT * FROM dbo.Player WHERE Name = {0}", name)
                     .Include(p => p.OwnedItem)
                     .ToList();

        foreach(var p in list)
        {
          Console.WriteLine($"{p.Name} {p.PlayerId}");
        }
                    
        // String Interpolation C#6.0
        var list2 = db.Players
                      .FromSqlInterpolated($"SELECT * FROM dbo.Player WHERE Name = {name}");

        foreach (var p in list2)
        {
          Console.WriteLine($"{p.Name} {p.PlayerId}");
        }
      }

      // ExecuteSqlCommand (Non-Query SQL)
      {
        Player p = db.Players.Single(p => p.Name == "Faker");
        string prevName = "Faker";
        string afterName = "Faker_New";
        db.Database.ExecuteSqlInterpolated($"UPDATE dbo.Player SET Name={afterName} WHERE Name={prevName}");

        db.Entry(p).Reload();
      }
    }
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/96.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/96.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/97.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/97.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

# Logging
  - print SQL log in console

### Test
* AppDbContext.cs
{% highlight C# %}
  public static readonly ILoggerFactory MyLoggerFactory = LoggerFactory.Create(builder => { builder.AddConsole(); });

  protected override void OnConfiguring(DbContextOptionsBuilder options)
  {
    options
      .UseLoggerFactory(MyLoggerFactory)
      .UseSqlServer(ConnectionString);
  }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/efcore_mmo_efcore/98.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/98.jpg"></a>
  <a href="/assets/img/posts/efcore_mmo_efcore/99.jpg"><img src="/assets/img/posts/efcore_mmo_efcore/99.jpg"></a>
	<figcaption>MMO EFCore</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
