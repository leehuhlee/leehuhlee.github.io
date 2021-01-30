---
layout: post
title: "[ASP.Net] Razor MVC"
date: 2021-01-30
excerpt: "Razor MVC"
tags: [C#, DotNet, ASP.Net]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## MVC
* M
  - Model
  - defines Data  
* V
  - View
  - defines UI
* C
  - Controller
  - defines Action


## Code
* Controllers\HomeController.cs
{% highlight C# %}
  using HelloEmpty.Models;
  using Microsoft.AspNetCore.Mvc;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace HelloEmpty.Controllers
  {
    public class HomeController : Controller
    {
      public IActionResult Index()
      {
        HelloMessage msg = new HelloMessage()
        {
          Message = "Welcome to Asp.Net MVC!"
        };

        ViewBag.Noti = "Input message and click Submit";

        return View(msg);
      }

      [HttpPost]
      public IActionResult Index(HelloMessage obj)
      {
        ViewBag.Noti = "Message Changed";

        return View(obj);
      }
    }
  }
{% endhighlight %}

* Models\HelloMessage.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace HelloEmptyMVC.Models
  {
    public class HelloMessage
    {
      public string Message { get; set; }
    }
  }
{% endhighlight %}

* Views\Home\Index.cshtml
{% highlight C# %}
  @model HelloEmptyMVC.Models.HelloMessage
  <html>
  <head>
    <title>Hello MVC!</title>
  </head>
  <body>
    <h1>@Model.Message</h1>
    <hr/>
    <h2>@ViewBag.Noti</h2>

    <form asp-controller="Home" asp-action="Index" method="post">
      <label asp-for="Message">Enter Message</label>
      <br/>
      <input type="text" asp-for="Message" />
      <br />
      <button type="submit">Submit</button>
    </form>
  </body>
  </html>
{% endhighlight %}

* Views\_ViewImports.cshtml
{% highlight C# %}
  @using HelloEmptyMVC
  @using HelloEmptyMVC.Models
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
{% endhighlight %}

* Startup.cs
{% highlight C# %}
  using Microsoft.AspNetCore.Builder;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.AspNetCore.Http;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace HelloEmptyMVC
  {
    public class Startup
    {
      // This method gets called by the runtime. Use this method to add services to the container.
      // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
      public void ConfigureServices(IServiceCollection services)
      {
        services.AddControllersWithViews();
      }

      // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
      public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
      {
        if (env.IsDevelopment())
        {
          app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
          endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
        });
      }
    }
  }
{% endhighlight %}


## ViewBag.Noti
* ViewBag.Noti
  - Before return View from Controller, You need input data in ViewBag to use it.
{% highlight C# %}
  ViewBag.Noti = "Input message and click Submit";
{% endhighlight %}


## Anchor Tag Helper in ASP.NET Core
  -  By convention, the attribute names are prefixed with asp-. 
  - If the asp-action attribute value is Index, then no action is appended to the URL, leading to the invocation of the default Index action. The action specified (or defaulted), must exist in the controller referenced in asp-controller.
* asp-controller
  - The `asp-controller` attribute assigns the controller used for generating the URL.
  - If the asp-controller attribute is specified and asp-action isn't, the default asp-action value is the controller action associated with the currently executing view.
* asp-action
  - The asp-action attribute value represents the controller action name included in the generated href attribute.
  - If no asp-controller attribute is specified, the default controller calling the view executing the current view is used.
{% highlight C# %}
  <form asp-controller="Home" asp-action="Index" method="post">
{% endhighlight %}

* asp-for
  - The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.
{% highlight C# %}
  <label asp-for="Message">Enter Message</label>
  <input type="text" asp-for="Message" />
{% endhighlight %}


# The Startup class

## ConfiguresServices
* ConfigureServices method 
  - Optionally includes a ConfigureServices method to configure the app's services. 
  - A service is a reusable component that provides app functionality. 
  - Services are registered in ConfigureServices and consumed across the app via dependency injection (DI) or ApplicationServices.
* MvcServiceCollectionExtensions.AddControllersWithViews Method
  - Adds services for controllers to the specified IServiceCollection. 
  - This method will not register services used for pages.
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddControllersWithViews();
  }
{% endhighlight %}


## Configure
* Configure method
  - Includes a Configure method to create the app's request processing pipeline.
* MapControllerRoute
  - Routing to controller actions in ASP.NET Core
  - Inside the call to UseEndpoints, MapControllerRoute is used to create a single route. 
  - The single route is named default route. 
  - Most apps with controllers and views use a route template similar to the default route. 
  - REST APIs should use attribute routing.
* default
  - Using this `default` route, the URL path:<br>
    /Products/List maps to the ProductsController.List action.<br>
    /Blog/Article/17 maps to BlogController.Article and typically model binds the id parameter to 17.
  - This mapping:<br>
    Is based on the controller and action names only.<br>
    Isn't based on namespaces, source file locations, or method parameters.
* Pattern Syntax
  - {controller=Home} defines Home as the default controller.
  - {action=Index} defines Index as the default action.
  - The ? character in {id?} defines id as optional.
  - Default and optional route parameters don't need to be present in the URL path for a match. See Route Template Reference for a detailed description of route template syntax.
  - Matches the URL path /.
  - Produces the route values { controller = Home, action = Index }.<br>
  The values for `controller` and `action` make use of the default values. id doesn't produce a value since there's no corresponding segment in the URL path. / only matches if there exists a HomeController and Index action


{% highlight C# %}
  public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
  {
    ...
    app.UseEndpoints(endpoints =>
    {
      endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    });
  }
{% endhighlight %}



## Result
<figure class="half">
  <a href="/assets/img/posts/aspdotnet_mvc/0.jpg"><img src="/assets/img/posts/aspdotnet_mvc/0.jpg"></a>
  <a href="/assets/img/posts/aspdotnet_mvc/1.jpg"><img src="/assets/img/posts/aspdotnet_mvc/1.jpg"></a>
	<figcaption>ASP.NET MVC</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
