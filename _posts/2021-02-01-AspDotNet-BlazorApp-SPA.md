---
layout: post
title: "[ASP.Net] Blazor Server SPA"
date: 2021-02-01
excerpt: "SPA, Router"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


# SPA Layout

## Program.cs
* Main
  - Call CreateHostBuilder
{% highlight C# %}
  public static void Main(string[] args)
  {
    CreateHostBuilder(args).Build().Run();
  }
{% endhighlight %}

* CreateHostBuilder
  - Call Startup class
{% highlight C# %}
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
          webBuilder.UseStartup<Startup>();
        });
{% endhighlight %}


## Startup.cs
* Startup
  - Call `_Host.cshtml`
{% highlight C# %}  
  public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
  {
    ...
    app.UseEndpoints(endpoints =>
    {
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
    });
  }
{% endhighlight %}


## _Host.cshtml
  - Call `App.razor`
{% highlight C# %}
  <app>
    <component type="typeof(App)" render-mode="ServerPrerendered" />
  </app>
{% endhighlight %}

## App.razor
  - Call `MainLayout.razor`
{% highlight C# %}
  <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
{% endhighlight %}

* Router Componenet
  - AppAssembly: Gets or sets the assembly that should be searched for components matching the URI.
  - Found: Gets or sets the content to display when a match is found for the requested route.
  - NotFound: Gets or sets the content to display when no match is found for the requested route.
{% highlight C# %}
  public class Router : IComponent, IHandleAfterRender, IDisposable
  {
    public Router();

    ...

    [Parameter]
    public Assembly AppAssembly { get; set; }

    [Parameter]
    public RenderFragment<RouteData> Found { get; set; }

    [Parameter]
    public RenderFragment NotFound { get; set; }

    public void Attach(RenderHandle renderHandle);
    public void Dispose();
    public Task SetParametersAsync(ParameterView parameters);
  }
{% endhighlight %}


## MainLayout.razor
  - Call `NavMenu.razor`
{% highlight C# %}
  <div class="sidebar">
    <NavMenu />
  </div>
{% endhighlight %}

  - Call `Body`
{% highlight C# %}
  <div class="content px-4">
    @Body
  </div>
{% endhighlight %}

* Body Property
  - Gets the content to be rendered inside the layout.
{% highlight C# %}
  public RenderFragment Body { get; set; }
{% endhighlight %}


## NavMenu.razor
* Redirect Web
  - For mobile, side Menu can be a toggle
{% highlight C# %}
  <div class="top-row pl-4 navbar navbar-dark">
    <a class="navbar-brand" href="">BlazorSPA</a>
    <button class="navbar-toggler" @onclick="ToggleNavMenu">
        <span class="navbar-toggler-icon"></span>
    </button>
  </div>
{% endhighlight %}


# Rout

## URI
* Pages\_Host.cshtml
  - define how to rout 
{% highlight C# %}
  <base href="~/" />
{% endhighlight %}

## Navigation
* NavigationManager
  - You can use not only `<a>`Tag, but also `NavigationManager`
  - Navigates to the specified URI
{% highlight C# %}
  public void NavigateTo(string uri, bool forceLoad = false);
{% endhighlight %}

* Pages\Counter.razor
  - Use `Defendency Injection`
{% highlight C# %}
  @page "/counter"
  ...
  @inject NavigationManager navManager
  ...

  <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
  <a class="btn btn-primaty" href="FetchData">Go FetchData</a>

  @code {
    private void IncrementCount()
    {
      CurrentCount++;
      navManager.NavigateTo("FetchData");
    }
    ...
  }
{% endhighlight %}


<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-NavigationManager.mp4" frameborder="0"> </iframe>

## URI Utilizing
* @page
  - You can use URI components value
{% highlight C# %}
  @page "/counter"
  @page "/counter/{CurrentCount:int}"
  ...
  <p>Current count: @CurrentCount</p>
  ...

  @code {
    private void IncrementCount()
    {
        CurrentCount++;
        navManager.NavigateTo("FetchData");
    }

    [Parameter]
    public int CurrentCount { get; set; }
  }
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/3.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/6.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>


# Page Layout

## MainLayout.razor
  - Base Layout in Blazor
{% highlight C# %}
  @inherits LayoutComponentBase
{% endhighlight %}

* LayoutComponentBase
  - Optional base class for components that represent a layout. 
  - Alternatively, components may implement Microsoft.AspNetCore.Components.IComponent directly and declare their own parameter named Microsoft.AspNetCore.Components.LayoutComponentBase.Body.
{% highlight C# %}    
  public abstract class LayoutComponentBase : ComponentBase
{% endhighlight %}

## Layout Customizing
* Shared
  - Create new layout by razor component
  - link this layout in razor component of `Pages` by `@layout`
  - Shared\MainLayout2.razor
{% highlight C# %} 
  ...
  <a href="https://docs.microsoft.com/aspnet/" target="_blank">Layout2</a>
  ...
{% endhighlight %}

  - Pages\Counter.razor
{% highlight C# %} 
  ...
  @layout MainLayout2 
  ...
{% endhighlight %}

* Pages
  - link this layout in `[folder]\_imports.razor` by `@layout`
  - It changes all layout of the folder
  - Pages\_imports.razor
{% highlight C# %} 
  @layout MainLayout2
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/aspdotnet_blazorapp/7.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/7.jpg"></a>
  <a href="/assets/img/posts/aspdotnet_blazorapp/8.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/8.jpg"></a>
  <a href="/assets/img/posts/aspdotnet_blazorapp/9.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/9.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
