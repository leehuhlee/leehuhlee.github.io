---
layout: post
title: "[Blazor] Ranking APP"
date: 2021-02-04
excerpt: "Database in Entity Framework"
tags: [C#, DotNet, ASP.Net, Blazor, Entity Framework]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Create Project 
* Blazor Server App
  - [Auth]-[Individual User Account]-[Save in User Account App]
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/0.jpg"><img src="/assets/img/posts/blazor_rankingapp/0.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>


## Active Database
* Startup.cs
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlServer(
        Configuration.GetConnectionString("DefaultConnection")));
      ...
  }
{% endhighlight %}


## Create a new Database
* RankingDB
  - Create in Local DB
  - Click [Property]
  - Copy context in [Link String]
<figure class="third">
  <a href="/assets/img/posts/blazor_rankingapp/11.jpg"><img src="/assets/img/posts/blazor_rankingapp/11.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/12.jpg"><img src="/assets/img/posts/blazor_rankingapp/12.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/5.jpg"><img src="/assets/img/posts/blazor_rankingapp/5.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>
  
  - Paste in `appsetting.json`
{% highlight C# %}
  {
    "ConnectionStrings": {
      "DefaultConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=RankingDB;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False"
    },
  ...
{% endhighlight %}


## Register User
* Register User in Web
  - Execute the solution
  - Click [Register]
  - Fill forms and Click [Register] button
  - If you register first user, then click [Migrations Apply] button then the button will be change to [Migrations Applied]
  - Press [F5] on Keyborad or refresh button in web
<figure class="third">
  <a href="/assets/img/posts/blazor_rankingapp/8.jpg"><img src="/assets/img/posts/blazor_rankingapp/8.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/3.jpg"><img src="/assets/img/posts/blazor_rankingapp/3.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/1.jpg"><img src="/assets/img/posts/blazor_rankingapp/1.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

  - Click [Click here to comfirm your account], then your email will be comfirmed
  - Login with this account
<figure class="third">
  <a href="/assets/img/posts/blazor_rankingapp/2.jpg"><img src="/assets/img/posts/blazor_rankingapp/2.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/6.jpg"><img src="/assets/img/posts/blazor_rankingapp/6.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/4.jpg"><img src="/assets/img/posts/blazor_rankingapp/4.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

  - You can see your log in data in topbar
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/7.jpg"><img src="/assets/img/posts/blazor_rankingapp/7.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>


## Check User
* Check User in Database
  - Open `dbo.AspNetUsers` Data
  - You can check your registed account
<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/9.jpg"><img src="/assets/img/posts/blazor_rankingapp/9.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/13.jpg"><img src="/assets/img/posts/blazor_rankingapp/13.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/10.jpg"><img src="/assets/img/posts/blazor_rankingapp/10.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>


## Migration
* Models
  - Create `Data\Models` folder
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/18.jpg"><img src="/assets/img/posts/blazor_rankingapp/18.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

* Data\Models\GameResult.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace RankingApp.Data.Models
  {
    public class GameResult
    {
      public int Id { get; set; }
      public int UserId { get; set; }
      public string UserName { get; set; }
      public int Score { get; set; }
      public DateTime Date { get; set; }
    }
  }
{% endhighlight %}

* Data\ApplicationDbContext.cs
{% highlight C# %}
  public DbSet<GameResult> GameResults { get; set; }
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/14.jpg"><img src="/assets/img/posts/blazor_rankingapp/14.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/15.jpg"><img src="/assets/img/posts/blazor_rankingapp/15.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

* Input Data in Database directly
<figure class="third">
  <a href="/assets/img/posts/blazor_rankingapp/16.jpg"><img src="/assets/img/posts/blazor_rankingapp/16.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/19.jpg"><img src="/assets/img/posts/blazor_rankingapp/19.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/17.jpg"><img src="/assets/img/posts/blazor_rankingapp/17.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>


## Database Version Management
* Up()
  - Builds operations that will migrate the database `up`
  - That is, builds the operations that will take the database from the state left in by the previous migration so that it is up-to-date with regard to migartion
  - This method must to be overridden in each class the inherits from `Migration`
{% highlight C# %}
  protected override void Up(MigrationBuilder migrationBuilder)
  {
    ...
  }
{% endhighlight %}

* Down()
  - Builds operations that will migrate the database `down`
  - That is, builds the operations that will take the database from the state left in by the this migration so that it returns to the state that it was in before this migration was applied.
  - This method must to be overridden in each class the inherits from `Migration` if both `up` and `down` migrations are to be supported. If it is not overridden, then calling it will throw and it will not to be possible to migrate in the `down` direction.
{% highlight C# %}
  protected override void Down(MigrationBuilder migrationBuilder)
  {
    ...
  }
{% endhighlight %}


## Authorization
* AuthorizeView
  - Displays differing content depending on the user's authorization status
* Authorized
  - The content that will be displayed if the user is authorized.
* NotAuthorized
  - The content that will be displayed if the user is not authorized.
{% highlight C# %}
  <AuthorizeView>
    <Authorized>
      ...
    </Authorized>
    <NotAuthorized>
      ...
    </NotAuthorized>
  </AuthorizeView>
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/20.jpg"><img src="/assets/img/posts/blazor_rankingapp/20.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/21.jpg"><img src="/assets/img/posts/blazor_rankingapp/21.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

# CRUD
* FormResult Method
  - Creates a System.Threading.Tasks.Task`1 that's completed successfully with the specified result.

## CREATE
{% highlight C# %}
  public Task<GameResult> AddGameResult(GameResult gameResult)
  {
    _context.GameResults.Add(gameResult);
    _context.SaveChanges();

    return Task.FromResult(gameResult);
  }
{% endhighlight %}

* SaveChanges Method
  - Saves all changes made in this context to the database.
  - This method will automatically call Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges to discover any changes to entity instances before saving to the underlying database.
  - This can be disabled via Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled.

{% highlight C# %}
  void AddGameResult()
  {
    _showPopup = true;
    _gameResult = new GameResult() { id = 0 };
  }

  async Task SaveGameResult()
  {
    if (_gameResult.id == 0)
    {
      _gameResult.Date = DateTime.Now;
      var result = RankingService.AddGameResult(_gameResult);
    }
    ...
    _gameResults = await RankingService.GetGameResultsAsync();
    _showPopup = false;
  }
{% endhighlight %}


## READ
{% highlight C# %}
  public Task<List<GameResult>> GetGameResultsAsync()
  {
    List<GameResult> results = _context.GameResults
      .OrderByDescending(item => item.Score)
      .ToList();
    
    return Task.FromResult(results);
  }
{% endhighlight %}

{% highlight C# %}
  protected override async Task OnInitializedAsync()
  {
    _gameResults = await RankingService.GetGameResultsAsync();
  }
{% endhighlight %}


## UPDATE
{% highlight C# %}
  public Task<bool> UpdateGameResult(GameResult gameResult)
  {
    var findResult = _context.GameResults
      .Where(x => x.Id == gameResult.Id)
      .FirstOrDefault();

    if (findResult == null)
      return Task.FromResult(false) ;

    findResult.UserName = gameResult.UserName;
    findResult.Score = gameResult.Score;
    _context.SaveChanges();

    return Task.FromResult(true);
  }
{% endhighlight %}

{% highlight C# %}
  void UpdateGameResult(GameResult gameResult)
  {
    _showPopup = true;
    _gameResult = gameResult;
  }

  async Task SaveGameResult()
  {
    if (_gameResult.Id == 0)
    ...
    else
    {
      var result = RankingService.UpdateGameResult(_gameResult);
    }

    _gameResults = await RankingService.GetGameResultsAsync();
    _showPopup = false;
  }
{% endhighlight %}


## DELETE
{% highlight C# %}
  public Task<bool> DeleteGameResult(GameResult gameResult)
  {
    var findResult = _context.GameResults
      .Where(x => x.Id == gameResult.Id)
      .FirstOrDefault();

    if (findResult == null)
      return Task.FromResult(false);

    _context.GameResults.Remove(gameResult);
    _context.SaveChanges();

    return Task.FromResult(true);
  }
{% endhighlight %}

{% highlight C# %}
  async Task DeleteGameResult(GameResult gameResult)
  {
    var result = RankingService.DeleteGameResult(gameResult);
    _gameResults = await RankingService.GetGameResultsAsync();
  }
{% endhighlight %}


# Result

## Create
<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-Create.mp4" frameborder="0"> </iframe>
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/22.jpg"><img src="/assets/img/posts/blazor_rankingapp/22.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>


## UPDATE
<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-Update.mp4" frameborder="0"> </iframe>


## DELETE
<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-Delete.mp4" frameborder="0"> </iframe>


[Download](https://github.com/leehuhlee/CShap){: .btn}
