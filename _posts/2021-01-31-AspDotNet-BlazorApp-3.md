---
layout: post
title: "[ASP.Net] Blazor Server App(3)"
date: 2021-01-31
excerpt: "Cascating Parameter"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Code

* Pages\User.razor
{% highlight C# %}
  @page "/user"
  @using  BlazorApp.Data;

  <h3>Online User</h3>

  <label>Theme Color:</label>
  <select class="form-control" @bind="_selectedColor">
    @foreach(var option in _options)
    {
      <option value="@option">
        @option
      </option>
    }
  </select>

  <CascadingValue Name="ThemeColor" Value="_selectedColor">
    <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
  </CascadingValue>

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

    string _selectedColor = "Green";
    List<string> _options = new List<string>() { "Green", "Red", "Blue" };

    List<UserData> _users = new List<UserData>();
    ShowUser _showUser;

    string _inputName;

    void AddUser()
    {
      _showUser.AddUser(new UserData() { Name = _inputName });
      _inputName = "";
    }

    void KickUser(UserData user)
    {
      _showUser.KickUser(user);
    }

    void CallbackTestFunc()
    {
      _inputName = "CallbackTest";
    }
  }
{% endhighlight %}

* Pages\ShowUser.razor
{% highlight C# %}
  @using BlazorApp.Data;

  <p>
    Users: <b>@Users.Count()</b>
  </p>

  <Counter></Counter>

  <br />

  <ul class="list-group">
    @foreach (UserData user in Users)
    {
      <li @key="user" class="list-group-item">
        <button type="button" class="btn btn-link" @onclick="(() => KickUser(user))">Kick</button>
        <label style="color:@_color">@user.Name</label>
      </li>
    }
  </ul>

  @code {
    [CascadingParameter(Name ="ThemeColor")]
    string _color { get; set; }  

    [Parameter]
    public List<UserData> Users { get; set; }

    [Parameter]
    public EventCallback CallbackTest { get; set; }

    protected override void OnInitialized()
    {
      Users.Add(new UserData() { Name = "Hanna" });
      Users.Add(new UserData() { Name = "Faker" });
      Users.Add(new UserData() { Name = "Deft" });
    }

    public void AddUser(UserData user)
    {
      Users.Add(user);
    }

    public void KickUser(UserData user)
    {
      Users.Remove(user);
      CallbackTest.InvokeAsync(null);
    }
  }
{% endhighlight %}

* Pages\Counter.razor
{% highlight C# %}
  @page "/counter"

  @using System.Threading;

  <h1 style="color:@_color">Counter</h1>

  <p>Current count: @currentCount</p>

  <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
  <button class="btn btn-secondary" @onclick="AutoIncrement">Auto Increment</button>


  @code {
    [CascadingParameter(Name = "ThemeColor")]
    string _color { get; set; }

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


## Cascating Parameter
* CascadingValue
  - You can assign parameter for children
  - be applied in children's children
* parent
  - define `Name` to match with child
  - specify variable in `Value` to use parameter
{% highlight C# %}
  <CascadingValue Name="ThemeColor" Value="_selectedColor">
    <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
  </CascadingValue>

  string _selectedColor = "Green";
{% endhighlight %}

* child
  - link parameter by `Name`
{% highlight C# %}
  [CascadingParameter(Name = "ThemeColor")]
  string _color { get; set; }
{% endhighlight %}


## Select Binding
* Select Binding
  - bind `@bind` expression and `value` attribute
{% highlight C# %}
  <select class="form-control" @bind="_selectedColor">
    @foreach(var option in _options)
    {
      <option value="@option">
        @option
      </option>
    }
  </select>
{% endhighlight %}


## Result
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Cascating-Parameter.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
