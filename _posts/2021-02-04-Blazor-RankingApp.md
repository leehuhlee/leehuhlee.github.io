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

<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-Create.mp4" frameborder="0"> </iframe>
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/22.jpg"><img src="/assets/img/posts/blazor_rankingapp/22.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

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

<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-Update.mp4" frameborder="0"> </iframe>


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

<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-Delete.mp4" frameborder="0"> </iframe>

# SharedData

## Add a new project
* SharedData
  - Class Library(.Net Core)

* Data\GameResult.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace SharedData.Models
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

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/23.jpg"><img src="/assets/img/posts/blazor_rankingapp/23.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

# PostMan

## Download
* Postman
  - Download <a href="https://www.postman.com/downloads/">Postman</a>
  - deactivate [Settings]-[Enable SSL certificate verification]

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/29.jpg"><img src="/assets/img/posts/blazor_rankingapp/29.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

# WebApi

## Add a new project
* WebApi
  - ASP.NET Core Web Application
  - template: API
  - package: Microsoft.EntityFrameworkCore, Microsoft.EntityFrameworkCore.Design, Microsoft.EntityFrameworkCore.SqlServer

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/25.jpg"><img src="/assets/img/posts/blazor_rankingapp/25.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

## Reference
* Dependency
  - references `SharedData`

## Database Connect
* Data\ApplicationDbContext.cs
{% highlight C# %}
  using Microsoft.EntityFrameworkCore;
  using SharedData.Models;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace WebApi.Data
  {
    public class ApplicationDbContext: DbContext
    {
      public DbSet<GameResult>GameResults { get; set; }
      public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options){ }
    }
  }
{% endhighlight %}

* Startup.cs
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    ...
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlServer(
        Configuration.GetConnectionString("DefaultConnection")));
  }
{% endhighlight %}

* appsettings.json
{% highlight C# %}
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=RankingApiDB;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False"
  },
  ...
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/24.jpg"><img src="/assets/img/posts/blazor_rankingapp/24.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

## Controllers\RankingController.cs
* REST (Representational State Transfer)
  - Reusing features from the original HTTP communication to create a data transmission rule

* ApiController feature
  - can return C# object
  - if return null, then 204 Response(No Content) in cli
  - string → text/plain
  - rest(int, bool) → application/json

* Read
  - ex: GET/ api/ ranking → get all items
  - ex: GET/ api/ ranking/1 → get item which is id=1

{% highlight C# %}
  using Microsoft.AspNetCore.Http;
  using Microsoft.AspNetCore.Mvc;
  using SharedData.Models;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using WebApi.Data;

  namespace WebApi.Controllers
  {
    
    [Route("api/[controller]")]
    [ApiController]
    public class RankingController : ControllerBase
    {
      ApplicationDbContext _context;

      public RankingController(ApplicationDbContext context)
      {
        _context = context;
      }

      // Read
      [HttpGet]
      public List<GameResult> GetGameResults()
      {
        List<GameResult> results = _context.GameResults
          .OrderByDescending(item => item.Score)
          .ToList();

        return results;
      }

      [HttpGet("{id}")]
      public GameResult GetGameResults(int id)
      {
        GameResult result = _context.GameResults
          .Where(item => item.Id == id)
          .FirstOrDefault();

        return result;
      }
    ...
{% endhighlight %}

  - Add data in `dbo.GameResults`

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/28.jpg"><img src="/assets/img/posts/blazor_rankingapp/28.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/26.jpg"><img src="/assets/img/posts/blazor_rankingapp/26.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/27.jpg"><img src="/assets/img/posts/blazor_rankingapp/27.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/30.jpg"><img src="/assets/img/posts/blazor_rankingapp/30.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/31.jpg"><img src="/assets/img/posts/blazor_rankingapp/31.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

* Create
  - ex: POST/ api/ ranking → Create item(real data in Body)

{% highlight C# %}
...
  [HttpPost]
  public GameResult AddGameResult([FromBody]GameResult gameResult)
  {
    _context.GameResults.Add(gameResult);
    _context.SaveChanges();

    return gameResult;
  }
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/32.jpg"><img src="/assets/img/posts/blazor_rankingapp/32.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

* Update
  - ex: PUT /api/ ranking(not used in web because of PUT authority problem) → request update item(real data in Body)

{% highlight C# %}
...
  [HttpPut]
  public bool UpdateGameResult([FromBody] GameResult gameResult)
  {
    var findResult = _context.GameResults
      .Where(x => x.Id == gameResult.Id)
      .FirstOrDefault();

    if (findResult == null)
      return false;

    findResult.UserName = gameResult.UserName;
    findResult.Score = gameResult.Score;
    _context.SaveChanges();

    return true;
  }
...
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/33.jpg"><img src="/assets/img/posts/blazor_rankingapp/33.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/34.jpg"><img src="/assets/img/posts/blazor_rankingapp/34.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

* Delete
  - DELETE/ api/ ranking/ 1(not used in web because of DELETE authority problem) → delete item which is id=1

{% highlight C# %}
...
  [HttpDelete("{id}")]
  public bool DeleteGameResult(int id)
  {
    var findResult = _context.GameResults
      .Where(x => x.Id == id)
      .FirstOrDefault();

    if (findResult == null)
      return false;

    _context.GameResults.Remove(findResult);
    _context.SaveChanges();

    return true;
  }
...
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/blazor_rankingapp/35.jpg"><img src="/assets/img/posts/blazor_rankingapp/35.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/36.jpg"><img src="/assets/img/posts/blazor_rankingapp/36.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

# Interlock RankingApp and WebApi

## RankingApp
* Reference
  - reference `SharedData`

* Folder Cleanup
  - delete `Data\Models`

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/37.jpg"><img src="/assets/img/posts/blazor_rankingapp/37.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

* Data\ApplicationDbContext.cs
{% highlight C# %}
  using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
  using Microsoft.EntityFrameworkCore;
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace RankingApp.Data
  {
    public class ApplicationDbContext : IdentityDbContext
    {
      public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
      {  }
    }
  }
{% endhighlight %}

* Data\Services\RankingService.cs
{% highlight C# %}
  using Newtonsoft.Json;
  using SharedData.Models;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Net.Http;
  using System.Text;
  using System.Threading.Tasks;

  namespace RankingApp.Data.Services
  {
    public class RankingService
    {
      HttpClient _httpClient;

      public RankingService(HttpClient client)
      {
        _httpClient = client;
      }

      // Create
      public async Task<GameResult> AddGameResult(GameResult gameResult)
      {
        string jsonStr = JsonConvert.SerializeObject(gameResult);
        var content = new StringContent(jsonStr, Encoding.UTF8, "application/json");
        var result = await _httpClient.PostAsync("api/ranking", content);

        if (result.IsSuccessStatusCode == false)
          throw new Exception("AddGameResult Failed");

        var resultContent = await result.Content.ReadAsStringAsync();
        GameResult resGameResult = JsonConvert.DeserializeObject<GameResult>(resultContent);

        return resGameResult;
      }

      // Read
      public async Task<List<GameResult>> GetGameResultsAsync()
      {
        var result = await _httpClient.GetAsync("api/ranking");
        var resultContent = await result.Content.ReadAsStringAsync();
        List<GameResult> resGameResults = JsonConvert.DeserializeObject<List<GameResult>>(resultContent);
        return resGameResults;
      }

      // Update
      public async Task<bool> UpdateGameResult(GameResult gameResult)
      {
        string jsonStr = JsonConvert.SerializeObject(gameResult);
        var content = new StringContent(jsonStr, Encoding.UTF8, "application/json");
        var result = await _httpClient.PutAsync("api/ranking", content);

        if (result.IsSuccessStatusCode == false)
          throw new Exception("AddGameResult Failed");

        return true;
      }

      // Delete
      public async Task<bool> DeleteGameResult(GameResult gameResult)
      {
        var result = await _httpClient.DeleteAsync($"api/ranking/{gameResult.Id}");

        if (result.IsSuccessStatusCode == false)
          throw new Exception("DeleteGameResult Faild");

        return true;
      }
    }
  }
{% endhighlight %}

* Razor.razor
{% highlight C# %}
...
  async Task SaveGameResult()
  {
    if (_gameResult.Id == 0)
    {
      _gameResult.Date = DateTime.Now;
      var result = await RankingService.AddGameResult(_gameResult);
    }
    else
    {
      var result = await RankingService.UpdateGameResult(_gameResult);
    }

    _gameResults = await RankingService.GetGameResultsAsync();
    _showPopup = false;
  }
{% endhighlight %}

* Startup.cs
  - use number in `Properties\launchSetting.json`

{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    ...
    services.AddScoped<AuthenticationStateProvider, RevalidatingIdentityAuthenticationStateProvider<IdentityUser>>();

    services.AddHttpClient<RankingService>(c =>
    {
      c.BaseAddress = new Uri("https://localhost:44313");
    });
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/38.jpg"><img src="/assets/img/posts/blazor_rankingapp/38.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

# Build

## Start several project at once
* Solution
  - [Property] - [Several start project]

<figure>
  <a href="/assets/img/posts/blazor_rankingapp/39.jpg"><img src="/assets/img/posts/blazor_rankingapp/39.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

# Result(CRUD)

## CREATE
<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-WebApi-Create.mp4" frameborder="0"> </iframe>

## READ
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/40.jpg"><img src="/assets/img/posts/blazor_rankingapp/40.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

## UPDATE
<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-WebApi-Update.mp4" frameborder="0"> </iframe>

## DELETE
<iframe width="560" height="315" src="/assets/video/posts/blazor_rankingapp/RankingApp-WebApi-Delete.mp4" frameborder="0"> </iframe>

# Interlock RankingApp, WebApi and Unity

## WebTest
* WebManager.cs
  - Put your WebApi Server Port number in `_baseUrl`

{% highlight C# %}
  using System;
  using System.Collections;
  using System.Collections.Generic;
  using System.Text;
  using UnityEngine;
  using UnityEngine.Networking;

  public class GameResult
  {
    public string userName;
    public int score;
  }

  public class WebManager : MonoBehaviour
  {
    string _baseUrl = "https://localhost:44358/api";

    void Start()
    {
      GameResult res = new GameResult()
      {
        userName = "Hanna",
        score = 999
      };

      SendPostRequest("ranking", res, (uwr) =>
      {
        Debug.Log("Post Success!");
      });

      SendGetAllRequest("ranking", (uwr) =>
      {
        Debug.Log("Get All Success!");
      });
    }

    public void SendPostRequest(String url, object obj, Action<UnityWebRequest> callback)
    {
      StartCoroutine(CoSendWebRequest(url, "POST", obj, callback));
    }

    public void SendGetAllRequest(String url, Action<UnityWebRequest> callback)
    {
      StartCoroutine(CoSendWebRequest(url, "GET", null, callback));
    }

    IEnumerator CoSendWebRequest(string url, string method, object obj, Action<UnityWebRequest> callback)
    {
      yield return null;

      string sendUrl = $"{_baseUrl}/{url}/";
      byte[] jsonBytes = null;

      if(obj != null)
      {
        string jsonStr = JsonUtility.ToJson(obj);
        jsonBytes = Encoding.UTF8.GetBytes(jsonStr);
      }

      var uwr = new UnityWebRequest(sendUrl, method);
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
        Debug.Log("Recy " + uwr.downloadHandler.text);
        callback.Invoke(uwr);
      }
    }
  }
{% endhighlight %}

* GameObject
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/41.jpg"><img src="/assets/img/posts/blazor_rankingapp/41.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

## Result
<figure>
  <a href="/assets/img/posts/blazor_rankingapp/42.jpg"><img src="/assets/img/posts/blazor_rankingapp/42.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
[Download](https://github.com/leehuhlee/Unity){: .btn}
