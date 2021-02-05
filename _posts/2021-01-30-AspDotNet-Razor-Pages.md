---
layout: post
title: "[ASP.Net] Razor Pages"
date: 2021-01-30
excerpt: "Razor Pages"
tags: [C#, DotNet, ASP.Net, Razor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Pages
* M
  - Model
  - defines Data  
* VC(or VM)
  - View and Controller(or Model)
  - defines UI
  - defines Action


## Code
* Models\HelloMessage.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace HelloEmptyRazor.Models
  {
    public class HelloMessage
    {
      public string Message { get; set; }
    }
  }
{% endhighlight %}

* Views\Index.cshtml
{% highlight C# %}
  @page
  @model HelloEmptyRazor.Pages.IndexModel
  <html>
  <head>
    <title>Hello Razor Pages</title>
  </head>
  <body>
    <h1>@Model.HelloMsg.Message</h1>
    <hr />
    <h2>@Model.Noti</h2>
    <form method="post">
      <label asp-for="HelloMsg.Message">Enter Message</label>
      <br />
      <input type="text" asp-for="HelloMsg.Message" />
      <br />
      <button type="submit">Submit</button>
    </form>
  </body>
  </html>
{% endhighlight %}

* Views\Index.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using HelloEmptyRazor.Models;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;

  namespace HelloEmptyRazor.Pages
  {
    public class IndexModel : PageModel
    {
      [BindProperty]
      public HelloMessage HelloMsg { get; set; }
      public string Noti { get; set; }

      public void OnGet()
      {
        this.HelloMsg = new HelloMessage()
        {
          Message = "Hello Razor Pages"
        };
      }

      public void OnPost()
      {
        this.Noti = "Message Changed";
      }
    }
  }
{% endhighlight %}

* Views\_ViewImports.cshtml
{% highlight C# %}
  @using HelloEmptyRazor
  @using HelloEmptyRazor.Models
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

  namespace HelloEmptyRazor
  {
    public class Startup
    {
      // This method gets called by the runtime. Use this method to add services to the container.
      // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
      public void ConfigureServices(IServiceCollection services)
      {
        services.AddRazorPages();
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
          endpoints.MapRazorPages();
        }
      }
    }
  }
{% endhighlight %}


## Differnce with MVC
* Index.cshtml
  - Razor Pages doesn't have `asp-controller` and `asp-action`, because it doesn't have a controller
{% highlight C# %}
  <form method="post">
{% endhighlight %}

* Startup.cs
  - Razor Pages serves by Pages.
  - Routing Method: Relative Path<br>
    To show a page, it search [Pages] folder and following path with filename
{% highlight C# %}
  services.AddRazorPages();
  app.UseEndpoints(endpoints =>
  {
    endpoints.MapRazorPages();
  }
{% endhighlight %}


## Result
<figure class="half">
  <a href="/assets/img/posts/aspdotnet_razor_pages/0.jpg"><img src="/assets/img/posts/aspdotnet_razor_pages/0.jpg"></a>
  <a href="/assets/img/posts/aspdotnet_razor_pages/1.jpg"><img src="/assets/img/posts/aspdotnet_razor_pages/1.jpg"></a>
	<figcaption>ASP.NET Razor Pages</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
