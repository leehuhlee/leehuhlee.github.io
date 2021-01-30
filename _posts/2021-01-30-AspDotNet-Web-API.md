---
layout: post
title: "[ASP.Net] Web API"
date: 2021-01-30
excerpt: "Web API"
tags: [C#, DotNet, ASP.Net, WebAPI]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Pages
* M
  - Model
  - defines Data  
* C
  - Controller
  - defines Action
* <b>No View!</b>
  - This is for data commnication
  - It doen't need UI


## Code
* Controllers\ValueController.cs
{% highlight C# %}
  using HelloEmptyWebAPI.Models;
  using Microsoft.AspNetCore.Http;
  using Microsoft.AspNetCore.Mvc;
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;

  namespace HelloEmptyWebAPI.Controllers
  {
    [Route("api/[controller]")]
    [ApiController]
    public class ValuesController : ControllerBase
    {
      [HttpGet]
      public List<HelloMessage> Get()
      {
        List<HelloMessage> messages = new List<HelloMessage>();
        messages.Add(new HelloMessage() { Message = "Hello Web API 1!" });
        messages.Add(new HelloMessage() { Message = "Hello Web API 2!" });
        messages.Add(new HelloMessage() { Message = "Hello Web API 3!" });

        return messages;
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

  namespace HelloEmptyWebAPI.Models
  {
    public class HelloMessage
    {
      public string Message { get; set; }
    }
  }
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
        services.AddControllers();
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
          endpoints.MapControllers();
        }
      }
    }
  }
{% endhighlight %}


## Route
* [Route(string)]
  - define how to match urls
  - In Web API, it match urls with controllers' name
{% highlight C# %}
  [Route("api/[controller]")]
{% endhighlight %}


## API Controller
* ApiController Attribute
  - The [ApiController] attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:<br>
    Attribute routing requirement<br>
    Automatic HTTP 400 responses<br>
    Binding source parameter inference<br>
    Multipart/form-data request inference<br>
    Problem details for error status codes<br>
{% highlight C# %}
  [ApiController]
{% endhighlight %}


## Startup.cs
* AddControllers
  - Adds services for controllers to the specified IServiceCollection. 
* MapControllers
  - Adds endpoints for controller actions to the IEndpointRouteBuilder without specifying any routes.
  - Rounting is in Controller
{% highlight C# %}
  services.AddControllers();
  app.UseEndpoints(endpoints =>
  {
    endpoints.MapControllers();
  });
{% endhighlight %}


## Result
<figure>
  <a href="/assets/img/posts/aspdotnet_webapi/0.jpg"><img src="/assets/img/posts/aspdotnet_webapi/0.jpg"></a>
	<figcaption>ASP.NET Web API</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
