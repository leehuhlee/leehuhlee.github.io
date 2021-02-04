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
* Models and Services
  - Create `Data\Models` folder
  - Create `Data\Services` folder
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
      public int id { get; set; }
      public int UserId { get; set; }
      public string UserName { get; set; }
      public int Score { get; set; }
      public DateTime Date { get; set; }
    }
  }
{% endhighlight %}

* Data\Services\RankingServices.cs
{% highlight C# %}
  using RankingApp.Data.Models;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace RankingApp.Data.Services
  {
    public class RankingService
    {
      ApplicationDbContext _context;
      public RankingService(ApplicationDbContext context)
      {
        _context = context;
      }

      public Task<List<GameResult>> GetGameResultsAsync()
      {
        List<GameResult> results = _context.GameResults
          .OrderByDescending(item => item.Score)
          .ToList();
        return Task.FromResult(results);
      }
    }
  }
{% endhighlight %}

* Data\ApplicationDbContext.cs
{% highlight C# %}
  public DbSet<GameResult> GameResults { get; set; }
{% endhighlight %}

* Startup.cs
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    ...
    services.AddScoped<RankingService>();
  }
{% endhighlight %}

* Pages\Ranking.razor
{% highlight C# %}
  @page "/ranking" 
  @using RankingApp.Data.Models 
  @using RankingApp.Data.Services

  @inject RankingService RankingService  

  <h3>Ranking</h3>

  @if(_gameResults == null)
  {
    <p><em>Loading...</em></p>
  }
  else
  {
    <table class="table">
      <thead>
        <tr>
          <th>UserName</th>
          <th>Score</th>
          <th>Date</th>
        </tr>
      </thead>
      <tbody>
        @foreach(var gameResult in _gameResults)
        {
          <tr>
            <td>@gameResult.UserName</td>
            <td>@gameResult.Score</td>
            <td>@gameResult.Date</td>
          </tr>
        }
      </tbody>
    </table>
  }

  @code {

    List<GameResult> _gameResults;

    protected override async Task OnInitializedAsync()
    {
      _gameResults = await RankingService.GetGameResultsAsync();
    }
  }
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


## FromResult
* FormResult method
  - Creates a System.Threading.Tasks.Task`1 that's completed successfully with the specified result.
{% highlight C# %}
  return Task.FromResult(results);
{% endhighlight %}


## Entity Framework ORM Syntax
* OrderByDescending(object)
  - Order by descending with object
* ToList
  - return to list
{% highlight C# %}
  public Task<List<GameResult>> GetGameResultsAsync()
  {
    List<GameResult> results = _context.GameResults
      .OrderByDescending(item => item.Score)
      .ToList();
    return Task.FromResult(results);
  }
{% endhighlight %}


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


[Download](https://github.com/leehuhlee/CShap){: .btn}
