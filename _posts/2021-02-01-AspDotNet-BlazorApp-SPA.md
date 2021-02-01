---
layout: post
title: "[ASP.Net] Blazor Server SPA"
date: 2021-02-01
excerpt: "SPA, Router"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Code
* Data\FoodService.razor
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace BlazorApp.Data
  {
    public class Food
    {
      public string Name { get; set; }
      public int Price { get; set; }
    }

    public interface IFoodService
    {
      IEnumerable<Food> GetFoods();
    }

    public class FoodService : IFoodService
    {
      public IEnumerable<Food> GetFoods()
      {
        List<Food> foods = new List<Food>()
        {
          new Food(){Name = "Bibimbap", Price=7000},
          new Food(){Name = "Kimbap", Price=3000},
          new Food(){Name = "Bossam", Price=9000}
        };

        return foods;
      }
    }
    
    public class FastFoodService : IFoodService
    {
      public IEnumerable<Food> GetFoods()
      {
        List<Food> foods = new List<Food>()
        {
          new Food(){Name = "Hamburger", Price=5000},
          new Food(){Name = "Fries", Price=2000},
        };

        return foods;
      }
    }

    public class PaymentService
    {
      IFoodService _service;

      public PaymentService(IFoodService service)
      {
        _service = service;
      }
    }

    public class SingletonService : IDisposable
    {
        public Guid ID { get; set; }
        public SingletonService()
        {
          ID = Guid.NewGuid();
        }

        public void Dispose()
        {
          Console.WriteLine("SingletonService Disposed");
        }
    }
    
    public class TransientService : IDisposable
    {
      public Guid ID { get; set; }
      public TransientService()
      {
        ID = Guid.NewGuid();
      }

      public void Dispose()
      {
        Console.WriteLine("SingletonService Disposed");
      }
    }
    
    public class ScopeService : IDisposable
    {
      public Guid ID { get; set; }
      public ScopeService()
      {
        ID = Guid.NewGuid();
      }

      public void Dispose()
      {
        Console.WriteLine("SingletonService Disposed");
      }
    }
  }
{% endhighlight %}

* Pages\Index.razor
{% highlight C# %}
  @page "/"

  @using BlazorApp.Data 
  @inject IFoodService foodService
  @inject PaymentService  paymentService

  @inject SingletonService singleton
  @inject TransientService transient
  @inject ScopeService scoped

  <div>
    @foreach (var food in foodService.GetFoods())
    {
      <div>@food.Name</div>
      <div>@food.Price</div>
    }
  </div>

  <div>
    <h1>Singleton</h1>
    Guid: @singleton.ID
    <h1>Transient</h1>
    Guid: @transient.ID
    <h1>Scoped</h1>
    Guid: @scoped.ID
  </div>

  @code{      
    protected override void OnInitialized(){ }
  }
{% endhighlight %}

* Startup.razor
{% highlight C# %}
  ...
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();

    services.AddSingleton<IFoodService, FastFoodService>();
    services.AddSingleton<PaymentService>();

    services.AddSingleton<SingletonService>();
    services.AddTransient<TransientService>();
    services.AddScoped<ScopeService>();
  }
  ...
{% endhighlight %}


## Dependency Injection
* Dependency
  - In OOP, the lower the dependence, the better
  - Rather than entering the class directly into the code, injecting dependencies using Startup.cs can reduce dependencies
* cs
  - Create Interface
  - Create Class that inherit the interface
{% highlight C# %}
  public interface IFoodService
  {
    IEnumerable<Food> GetFoods();
  }

  public class FoodService : IFoodService
  {
    public IEnumerable<Food> GetFoods()
    {
      List<Food> foods = new List<Food>()
      {
        new Food(){Name = "Bibimbap", Price=7000},
        new Food(){Name = "Kimbap", Price=3000},
        new Food(){Name = "Bossam", Price=9000}
      };

      return foods;
    }
  }
{% endhighlight %}

* Startup.cs
  - Start service in `ConfigureServices`
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddSingleton<IFoodService, FastFoodService>();
  }
{% endhighlight %}  

* cshtml
  - Inject the interface to use
{% highlight C# %}
  @inject IFoodService foodService
{% endhighlight %}   

* Difference with C++
  - When you use same interfacein different class, you don't need to service the interface again
  - Asp.Net will connect it atomatically
{% highlight C# %}
  public class PaymentService
  {
    IFoodService _service;

    public PaymentService(IFoodService service)
    {
      _service = service;
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddSingleton<PaymentService>();
{% endhighlight %} 

{% highlight C# %}
  @inject PaymentService  paymentService
{% endhighlight %} 


# Service lifetimes

## Singleton
  - Update every time server restart
{% highlight C# %}
  public class SingletonService : IDisposable
  {
    public Guid ID { get; set; }
    public SingletonService()
    {
      ID = Guid.NewGuid();
    }

    public void Dispose()
    {
      Console.WriteLine("SingletonService Disposed");
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddSingleton<SingletonService>();
{% endhighlight %} 

{% highlight C# %}
  @inject SingletonService singleton

  <h1>Singleton</h1>
  Guid: @singleton.ID
{% endhighlight %} 

## Transient
  - Update every time the page is requested
{% highlight C# %}
  public class TransientService : IDisposable
  {
    public Guid ID { get; set; }
    public TransientService()
    {
      ID = Guid.NewGuid();
    }

    public void Dispose()
    {
      Console.WriteLine("SingletonService Disposed");
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddTransient<TransientService>();
{% endhighlight %} 

{% highlight C# %}
  @inject TransientService transient

  <h1>Transient</h1>
  Guid: @transient.ID
{% endhighlight %} 

## Scoped
  - Update every time web connect
{% highlight C# %}
  public class ScopeService : IDisposable
  {
    public Guid ID { get; set; }
    public ScopeService()
    {
      ID = Guid.NewGuid();
    }

    public void Dispose()
    {
      Console.WriteLine("SingletonService Disposed");
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddScoped<ScopeService>();
{% endhighlight %} 

{% highlight C# %}
  @inject ScopeService scoped
  
  <h1>Scoped</h1>
  Guid: @scoped.ID
{% endhighlight %} 


# Result

## Defendency Injection
<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/3.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/3.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>

## Singleton
<figure class="half">
  <a href="/assets/img/posts/aspdotnet_blazorapp/4.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/4.jpg"></a>
  <a href="/assets/img/posts/aspdotnet_blazorapp/5.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/5.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>

## Transient
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Transient.mp4" frameborder="0"> </iframe>

## Scoped
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Scoped.mp4" frameborder="0"> </iframe>


[Download](https://github.com/leehuhlee/CShap){: .btn}
