---
layout: post
title: "[Entity Framework Core] Contoso Pets Databse Provider"
date: 2021-01-29
excerpt: "Entity Famework Core and Sqlite"
tags: [C#, DotNet, Entity Framework Core, Sqlite]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/Entity-Framework-Core-101">Entity Framework Core 101</a></center>


## Database Providers
* Database Providers
  - Entity Framework Core can access many different databases through plug-in libraries called database providers.
  - Most database providers for EF Core are distributed as NuGet packages.
* Adding a database provider 
  - Once installed, you will configure the provider in your DbContext, either in the OnConfiguring method or in the AddDbContext method if you are using a dependency injection container.
  - Database providers can extend EF Core to enable functionality unique to specific databases. Some concepts are common to most databases, and are included in the primary EF Core components. Such concepts include expressing queries in LINQ, transactions, and tracking changes to objects once they are loaded from the database. 
  - Some concepts are specific to a particular provider. For example, the SQL Server provider allows you to configure memory-optimized tables (a feature specific to SQL Server). 
  - Other concepts are specific to a class of providers. For example, EF Core providers for relational databases build on the common Microsoft.EntityFrameworkCore.Relational library, which provides APIs for configuring table and column mappings, foreign key constraints, etc. 
<figure>
  <a href="/assets/img/posts/efcore_contosopets_dbprovider/0.jpg"><img src="/assets/img/posts/efcore_contosopets_dbprovider/0.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets Database Provider</figcaption>
</figure>


## Code
* Startup.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using ContosoPets.Ui.Data;
  using ContosoPets.Ui.Models;
  using Microsoft.AspNetCore.Builder;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.AspNetCore.HttpsPolicy;
  using Microsoft.EntityFrameworkCore;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  namespace ContosoPets.Ui
  {
    public class Startup
    {
      public Startup(IConfiguration configuration)
      {
        Configuration = configuration;
      }

      public IConfiguration Configuration { get; }

      // This method gets called by the runtime. Use this method to add services to the container.
      public void ConfigureServices(IServiceCollection services)
      {
        services.AddRazorPages();
        services.AddDbContext<ContosoPetsContext>(options =>
          options.UseSqlite("Data Source=ContosoPets.db"));
      }

      // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
      public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
      {
        if (env.IsDevelopment())
        {
          app.UseDeveloperExceptionPage();
        }
        else
        {
          app.UseExceptionHandler("/Error");
          // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
          app.UseHsts();
        }

        app.UseHttpsRedirection();
        app.UseStaticFiles();

        app.UseRouting();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
          endpoints.MapRazorPages();
        });
      }
    } 
  }
{% endhighlight %}


## Sqlite
* Sqlite
  - SQLite is a C-language library that implements a small, fast, self-contained, high-reliability, full-featured, SQL database engine. 
  - SQLite is the most used database engine in the world. 
  - SQLite is built into all mobile phones and most computers and comes bundled inside countless other applications that people use every day.
  - The SQLite file format is stable, cross-platform, and backwards compatible and the developers pledge to keep it that way through the year 2050. 
  - SQLite database files are commonly used as containers to transfer rich content between systems and as a long-term archival format for data.
* DB browser for Sqlite
  - Download: <a href="https://sqlitebrowser.org/dl/">DB browser for Sqlite</a>
  - Show your Database on GUI


# Result

## Create
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets_dbprovider/1.jpg"><img src="/assets/img/posts/efcore_contosopets_dbprovider/1.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_dbprovider/2.jpg"><img src="/assets/img/posts/efcore_contosopets_dbprovider/2.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_dbprovider/3.jpg"><img src="/assets/img/posts/efcore_contosopets_dbprovider/3.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets Database Provider</figcaption>
</figure>


## Sqlite 
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
  services.AddRazorPages();
  services.AddDbContext<ContosoPetsContext>(options =>
    options.UseSqlite("Data Source=ContosoPets.db"));
  }
{% endhighlight %}
<figure class="half">
  <a href="/assets/img/posts/efcore_contosopets_dbprovider/5.jpg"><img src="/assets/img/posts/efcore_contosopets_dbprovider/5.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_dbprovider/4.jpg"><img src="/assets/img/posts/efcore_contosopets_dbprovider/4.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets Database Provider</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
