---
layout: post
title: "[ASP.Net] Blazor Server App(2)"
date: 2021-01-31
excerpt: "Parameter, ref, EventCallBack"
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

  <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>

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

  <br />

  <ul class="list-group">
    @foreach (UserData user in Users)
    {
      <li @key="user" class="list-group-item">
        <button type="button" class="btn btn-link" @onclick="(() => KickUser(user))">Kick</button>
        <label>@user.Name</label>
      </li>
    }
  </ul>

  @code {
    [Parameter]
    public List<UserData> Users { get; set; }

    [Parameter]
    public EventCallback CallbackTest { get; set; }
    //public Action CallbackTest{ get; set; }

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
      //StateHasChanged();
    }
  }
{% endhighlight %}


## Parameter
  - In this post, `ShowUser.cshtml` is child and `User.cshtml` is parent
  - To use parent's variable or function in child
* Parent
  - link the variable with child's parameter name 
{% highlight C# %}
  <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
{% endhighlight %}

* Child
  - define variable and spedify it is a parameter by `[Parameter]`
{% highlight C# %}
  [Parameter]
  public List<UserData> Users { get; set; }
{% endhighlight %}


## ref
  - In this post, `ShowUser.cshtml` is child and `User.cshtml` is parent
  - To use child's variable or function in parent
* Parent
  - define variable with child class name
{% highlight C# %}
  <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
  ShowUser _showUser;
{% endhighlight %}


## EventCallBack
* EventCallback struckt
  - A bound event handler delegate
{% highlight C# %}
  public EventCallback CallbackTest { get; set; }
{% endhighlight %}

* EventCallback.InvokeAsync Method
  - Invokes the delegate associated with this binding and dispatches an event notification to the appropriate component.
{% highlight C# %}
  public void KickUser(UserData user)
  {
    Users.Remove(user);
    CallbackTest.InvokeAsync(null);
  }
{% endhighlight %}

* Diffence with `Action`
  - Prefer the strongly typed EventCallback<T>, which provides better error feedback to users of the component. 
  - Similar to other UI event handlers, specifying the event parameter is optional. Use EventCallback when there's no value passed to the callback.
{% highlight C# %}
  public Action CallbackTest { get; set; }
  public void KickUser(UserData user)
  {
    Users.Remove(user);
    CallbackTest.Invoke();
    StateHasChanged();
  }
{% endhighlight %}


## Result
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Parameter-Ref-EventCallback.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
