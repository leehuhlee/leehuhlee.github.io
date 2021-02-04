---
layout: post
title: "[Blazor] Ranking APP(1)"
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
<figure class="third">
  <a href="/assets/img/posts/blazor_rankingapp/9.jpg"><img src="/assets/img/posts/blazor_rankingapp/9.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/13.jpg"><img src="/assets/img/posts/blazor_rankingapp/13.jpg"></a>
  <a href="/assets/img/posts/blazor_rankingapp/10.jpg"><img src="/assets/img/posts/blazor_rankingapp/10.jpg"></a>
	<figcaption>Blazor Ranking App</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
