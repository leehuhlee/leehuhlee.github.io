---
layout: post
title: "[ASP.Net] Blazor Server APP(7)"
date: 2021-02-03
excerpt: "State Management"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Code
* Data\CounterState.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace BlazorApp.Data
  {
    public class CounterState
    {
      int _count = 0;

      public Action OnStateChanged;

      public int Count
      {
        get
        {
          return _count;
        }

        set
        {
          _count = value;
          Refresh();
        }
      }

      void Refresh()
      {
        OnStateChanged?.Invoke();
      }
    }
  }
{% endhighlight %}

* Pages\Counter.razor
{% highlight C# %}
  @page "/counter"
  @inject BlazorApp.Data.CounterState CounterState

  @using System.Threading;

  <h1 style="color:@_color">Counter</h1>

  <p>Current count: @CounterState.Count</p>

  <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
  <button class="btn btn-secondary" @onclick="AutoIncrement">Auto Increment</button>

  @code {
    [CascadingParameter(Name = "ThemeColor")]
    string _color { get; set; }

    private void IncrementCount()
    {
      CounterState.Count++;
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

* Pages\Index.razor
{% highlight C# %}
  ...
  <Counter></Counter>
  ...
{% endhighlight %}

* Shared\NavMenu.razor
{% highlight C# %}
  @inject BlazorApp.Data.CounterState CounterState
  @implements IDisposable
  ...
  <div>
    <p style="color:white">Counter: @CounterState.Count</p>
  </div>

  @code {
    ...
    protected override void OnInitialized()
    {
      CounterState.OnStateChanged += onStateChanged;
    }

    void onStateChanged()
    {
      this.StateHasChanged();
    }

    void IDisposable.Dispose()
    {
      CounterState.OnStateChanged -= onStateChanged;
    }
  }
{% endhighlight %}

* Startup.cs
{% highlight C# %}
  ...
  public void ConfigureServices(IServiceCollection services)
  {
    ...
    services.AddScoped<CounterState>();
  }
  ...
{% endhighlight %}


## Action 
* Action Delegate
  - Encapsulates a method that has no parameters and does not return a value
  - You can change UI State by Invoke()
{% highlight C# %}
  public Action OnStateChanged;
{% endhighlight %}

## Value
* value
  - The value keyword is used to define the value being assigned by the set accessor.
{% highlight C# %}
  public int Count
  {
    get
    {
      return _count;
    }

    set
    {
      _count = value;
      Refresh();
    }
  }
{% endhighlight %}


## IDisposable
* Idisposable Interface
  - Provides a mechanism for releasing unmanaged resources
* Dispose()
  - Performs application-defined tasks associated with freeing, releasing, or resetting unmanaged resources
   

# Result
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-State-Management.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
