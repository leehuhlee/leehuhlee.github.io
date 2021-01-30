---
layout: post
title: "[ASP.Net] Blazor Server App"
date: 2021-01-30
excerpt: "Binding"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Blazor
* SPA
  - Single Page Application
  - Load HTML and extra files only one time
  - After first Loading, request by Signal(ex. AJAX) with no-reloading
<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/0.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/0.jpg"></a>
	<figcaption>ASP.NET Web API</figcaption>
</figure>

* .cshtml and .razor
  - .cshtml: Razor page file
  - .razor: Razor component file


## Code
* Data\UserData.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace BlazorApp.Data
  {
    public class UserData
    {
      public string Name { get; set; }
    }
  }
{% endhighlight %}

* Shared\NavMenu.razor
{% highlight C# %}
  <div class="top-row pl-4 navbar navbar-dark">
    <a class="navbar-brand" href="">BlazorApp</a>
    <button class="navbar-toggler" @onclick="ToggleNavMenu">
        <span class="navbar-toggler-icon"></span>
    </button>
  </div>

  <div class="@NavMenuCssClass" @onclick="ToggleNavMenu">
    <ul class="nav flex-column">
      <li class="nav-item px-3">
        <NavLink class="nav-link" href="" Match="NavLinkMatch.All">
          <span class="oi oi-home" aria-hidden="true"></span> Home
        </NavLink>
      </li>
      <li class="nav-item px-3">
        <NavLink class="nav-link" href="counter">
          <span class="oi oi-plus" aria-hidden="true"></span> Counter
        </NavLink>
      </li>
      <li class="nav-item px-3">
        <NavLink class="nav-link" href="fetchdata">
          <span class="oi oi-list-rich" aria-hidden="true"></span> Fetch data
        </NavLink>
      </li>
      <li class="nav-item px-3">
        <NavLink class="nav-link" href="user">
          <span class="oi oi-list-rich" aria-hidden="true"></span> User
        </NavLink>
      </li>
    </ul>
  </div>

  @code {
    private bool collapseNavMenu = true;

    private string NavMenuCssClass => collapseNavMenu ? "collapse" : null;

    private void ToggleNavMenu()
    {
      collapseNavMenu = !collapseNavMenu;
    }
  }
{% endhighlight %}

* Pages\User.razor
{% highlight C# %}
  @page "/user"
  @using  BlazorApp.Data;

  <h3>Online User</h3>

  <p>
    Users: <b>@_users.Count()</b>
  </p>

  <br />

  <ul class="list-group">
    @foreach(UserData user in _users)
    { 
      <li @key="user" class="list-group-item">
        <button type="button" class="btn btn-link" @onclick="(() => KickUser(user))">Kick</button>
        <label>@user.Name</label>
      </li>
    }
  </ul>

  <br />

  <div class="container">
    <div class="row">
      <div class="col">
        <input class="form-control" placeholder="Add User"@bind-value="_inputName" />
      </div>
      <div class="col">
        <button class="@_btnClass" type="button" @onclick="AddUser" disabled="@(_users.Count()>=5)">Add a User</button>
      </div>
    </div>
  </div>

  @code {
    List<UserData> _users = new List<UserData>();

    string _inputName;
    string _btnClass = "btn btn-primary";

    protected override void OnInitialized()
    {
      _users.Add(new UserData() { Name = "Hanna" });
      _users.Add(new UserData() { Name = "Faker" });
      _users.Add(new UserData() { Name = "Deft" });
      RefreshButton();
    }

    void AddUser()
    {
      _users.Add(new UserData() { Name = _inputName });
      _inputName = "";
      RefreshButton();
  }

    void KickUser(UserData user)
    {
      _users.Remove(user);
      RefreshButton();
    }

    void RefreshButton()
    {
      if (_users.Count() % 2 == 0)
        _btnClass = "btn btn-primary";
      else
        _btnClass = "btn btn-secondary";
    }
  }
{% endhighlight %}

* Pages\Counter.razor
{% highlight C# %}
  @page "/counter"

  @using System.Threading;

  <h1>Counter</h1>

  <p>Current count: @currentCount</p>

  <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
  <button class="btn btn-secondary" @onclick="AutoIncrement">Auto Increment</button>


  @code {
    private int currentCount = 0;

    private void IncrementCount()
    {
      currentCount++;
    }

    void AutoIncrement()
    {
      var timer = new Timer(x =>
      {
        InvokeAsync(() =>
        {
          IncrementCount();
          StateHasChanged();
        });
      }, null, 1000, 1000);
    }
  }
{% endhighlight %}


## Route
* @page
  - define how to match urls
  - method is same with Razor pages
{% highlight C# %}
  @page "/user"
{% endhighlight %}


## Binding
* @
  - specifies to use C#
  - You can make attribute or condition by this, also
{% highlight C# %}
  button class="@_btnClass"
  disabled="@(_users.Count()>=5)
{% endhighlight %}

* @key
  - The @key directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value. 
{% highlight C# %}
  <li @key="user" class="list-group-item">
{% endhighlight %}

* @bind-value
  - bind value by variable name
{% highlight C# %}
  @bind-value="_inputName"
{% endhighlight %}

* @code
  - C# code in razor file
  - seems like script in html file


## Lambda
* ()=>
  - is used for onclick method syntax
{% highlight C# %}
  @onclick="(() => KickUser(user))"
{% endhighlight %}


## System.Threading
* System.Threading Namespace
  - Provides classes and interfaces that enable multithreaded programming. 
  - In addition to classes for synchronizing thread activities and access to data (Mutex, Monitor, Interlocked, AutoResetEvent, and so on), this namespace includes a ThreadPool class that allows you to use a pool of system-supplied threads, and a Timer class that executes callback methods on thread pool threads.
* Timer Class
  - Provides a mechanism for executing a method on a thread pool thread at specified intervals. 
  - This class cannot be inherited.
* InvokeAsync(Action)
  - Executes the specified Action asynchronously on the thread the Dispatcher is associated with.
* StateHasChanged
  - Notifies the component that its state has changed. 
  - When applicable, this will cause the component to be re-rendered.
{% highlight C# %}
  @using System.Threading;
  void AutoIncrement()
  {
    var timer = new Timer(x =>
    {
      InvokeAsync(() =>
      {
        IncrementCount();
        StateHasChanged();
      });
    }, null, 1000, 1000);
  }
{% endhighlight %}


## Result
<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/0.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/0.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Create.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Delete.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-NotMore5.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Counter.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
