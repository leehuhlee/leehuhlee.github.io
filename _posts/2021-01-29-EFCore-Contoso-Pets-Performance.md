---
layout: post
title: "[Entity Framework Core] Contoso Pets Performence"
date: 2021-01-29
excerpt: "Entity Famework Core"
tags: [C#, DotNet, Entity Framework Core]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/Entity-Framework-Core-101">Entity Framework Core 101</a></center>


# Disable Tracking

## Code
* Products\Details.cshtml.cs
{% highlight C# %}
  public async Task<IActionResult> OnGetAsync(int? id)
  {
    if (id == null)
    {
      return NotFound();
    }

    Product = await _context.Products.AsNoTracking().FirstOrDefaultAsync(m => m.Id == id);

    if (Product == null)
    {
      return NotFound();
    }
    return Page();
  }
{% endhighlight %}

## AsNoTracking()
* DbExtensions.AsNoTracking Method
  - Returns a new query where the entities returned will not be cached in the DbContext or ObjectContext. 
  - This method works by calling the AsNoTracking method of the underlying query object.
{% highlight C# %}
  public async Task<IActionResult> OnGetAsync(int? id)
  {
    ...
    Product = await _context.Products.AsNoTracking().FirstOrDefaultAsync(m => m.Id == id);
    ...
  }
{% endhighlight %}


# Laoding Patterns

## Code
* Customers\Details.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.EntityFrameworkCore;
  using ContosoPets.Ui.Data;
  using ContosoPets.Ui.Models;

  namespace ContosoPets.Ui.Pages.Customers
  {
    public class DetailsModel : PageModel
    {
      private readonly ContosoPets.Ui.Data.ContosoPetsContext _context;

      public DetailsModel(ContosoPets.Ui.Data.ContosoPetsContext context)
      {
        _context = context;
      }

      public Customer Customer { get; set; }

      public async Task<IActionResult> OnGetAsync(int? id)
      {
        if (id == null)
        {
          return NotFound();
        }

        Customer = await _context.Customers
          // 1. Eager Loading///////////////////////////////
          .Include(c => c.Orders)
          .SingleAsync(c => c.Id == id);
          //////////////////////////////////////////////////

          // 2. Lazy Loading///////////////////////////////
          .SingleAsync(c => c.Id == id);
          //////////////////////////////////////////////////

        if (Customer == null)
        {
          return NotFound();
        }
        return Page();
      }
    }
  }
{% endhighlight %}

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
  using Microsoft.EntityFrameworkCore.Diagnostics;
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
        services.AddDbContextPool<ContosoPetsContext>(options =>
          // 1. Eager Loading ///////////////////////////////
          options.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0"));
          //////////////////////////////////////////////////
          
          // 2. Lazy Loading ///////////////////////////////
          options.UseLazyLoadingProxies().UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0"));
          //////////////////////////////////////////////////
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

* Models\Customer.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.ComponentModel.DataAnnotations;
  using System.Linq;
  using System.Threading.Tasks;

  namespace ContosoPets.Ui.Models
  {
    public partial class Customer
    {
      public Customer()
      {
        Orders = new HashSet<Order>();
      }

      public int Id { get; set; }
      [Required]
      public string FirstName { get; set; }
      [Required]
      public string LastName { get; set; }
      public string StreetAddress { get; set; }
      public string City { get; set; }
      public string StateOrProvinceAbbr { get; set; }
      public string Country { get; set; }
      public string PostalCode { get; set; }
      public string Phone { get; set; }
      public string Email { get; set; }

      // 1. Eager Loading///////////////////////////////
      public ICollection<Order> Orders { get; set; }    
      //////////////////////////////////////////////////

      // 2. Eager Loading///////////////////////////////
      public virtual ICollection<Order> Orders { get; set; }
      //////////////////////////////////////////////////
    }

}
{% endhighlight %}


## Database Context Pooling
* EntityFrameworkServiceCollectionExtensions.AddDbContextPool Method
  - Registers the given DbContext as a service in the IServiceCollection, and enables DbContext pooling for this registration.
  - DbContext pooling can increase performance in high-throughput scenarios by re-using context instances.
  - Note that when using pooling, the context configuration cannot change between uses, and scoped services injected into the context will only be resolved once from the initial scope. 
  - Use this method when using dependency injection in your application, such as with ASP.NET Core.
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    ...
    services.AddDbContextPool<ContosoPetsContext>(options =>
    ...
  }
{% endhighlight %}


## Eager Loading
* Eager Loading
  - Eager loading means that the related data is loaded from the database as part of the initial query.
* Eager Loading of Related Data
  - You can use the `Include` method to specify related data to be included in query results.
{% highlight C# %}
  public async Task<IActionResult> OnGetAsync(int? id)
  {
    ...
    Customer = await _context.Customers
      .Include(c => c.Orders
      .SingleAsync(c => c.Id == id);
    ...
  }

  public void ConfigureServices(IServiceCollection services)
  {
    ...
    options.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0"));
    ...
  }

  public ICollection<Order> Orders { get; set; }
{% endhighlight %}


## Lazy Loading
* Lazy Loading
  - Lazy loading means that the related data is transparently loaded from the database when the navigation property is accessed.
* Lazy loading with proxies
  - The simplest way to use lazy-loading is by installing the` Microsoft.EntityFrameworkCore.Proxies` package and enabling it with a call to `UseLazyLoadingProxies`.
<figure>
  <a href="/assets/img/posts/efcore_contosopets_performance/0.jpg"><img src="/assets/img/posts/efcore_contosopets_performance/0.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets Performance</figcaption>
</figure>
{% highlight C# %}  
  public async Task<IActionResult> OnGetAsync(int? id)
  {
    ...
    Customer = await _context.Customers
      .SingleAsync(c => c.Id == id);
    ...
  }

  public void ConfigureServices(IServiceCollection services)
  {
    ...
    options.UseLazyLoadingProxies().UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0"));
    ...
  }

  public virtual ICollection<Order> Orders { get; set; }
{% endhighlight %}


# SQL Interpolatd

## Code
* Pages\Products\Index.cshtml.cs
{% highlight C# %} 
  using System.Collections.Generic;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.EntityFrameworkCore;
  using ContosoPets.Ui.Data;
  using ContosoPets.Ui.Models;

  namespace ContosoPets.Ui.Pages.Products
  {
    public class IndexModel : PageModel
    {
      private readonly ContosoPetsContext _context;

      public IndexModel(ContosoPetsContext context)
      {
        _context = context;
      }

      public IList<Product> Product { get;set; }

      public async Task OnGetAsync()
      {
        decimal minPrice = 5.00m;
        Product = await _context.Products
          .FromSqlInterpolated($"SELECT * FROM dbo.Products WHERE Price > {minPrice}")
          .ToListAsync();
      }
    }
  }
{% endhighlight %}


## FromSqlInterpolatd
* RelationalQueryableExtensions.FromSqlInterpolated Method
  - Creates a LINQ query based on an interpolated string representing a SQL query.
  - If the database provider supports composing on the supplied SQL, you can compose on top of the raw SQL query using LINQ operators
  - As with any API that accepts SQL it is important to parameterize any user input to protect against a SQL injection attack. 
  - You can include interpolated parameter place holders in the SQL query string. 
  - Any interpolated parameter values you supply will automatically be converted to a DbParameter
{% highlight C# %} 
  public async Task OnGetAsync()
  {
    decimal minPrice = 5.00m;
    Product = await _context.Products
      .FromSqlInterpolated($"SELECT * FROM dbo.Products WHERE Price > {minPrice}")
      .ToListAsync();
  }
{% endhighlight %}


# Snapshot retreive

## Code
* Pages\Products\Delete.cshtml.cs
{% highlight C# %} 
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.EntityFrameworkCore;
  using ContosoPets.Ui.Data;
  using ContosoPets.Ui.Models;

  namespace ContosoPets.Ui.Pages.Products
  {
    public class DeleteModel : PageModel
    {
      private readonly ContosoPets.Ui.Data.ContosoPetsContext _context;

      public DeleteModel(ContosoPets.Ui.Data.ContosoPetsContext context)
      {
        _context = context;
      }

      [BindProperty]
      public Product Product { get; set; }

      public async Task<IActionResult> OnGetAsync(int? id)
      {
        if (id == null)
        {
          return NotFound();
        }

        Product = await _context.Products.FindAsync(id);

        if (Product == null)
        {
          return NotFound();
        }
        return Page();
      }

      public async Task<IActionResult> OnPostAsync(int? id)
      {
        if (id == null)
        {
          return NotFound();
        }

        Product = await _context.Products.FindAsync(id);

        if (Product != null)
        {
          _context.Products.Remove(Product);
          await _context.SaveChangesAsync();
        }

        return RedirectToPage("./Index");
      }
    }
  }
{% endhighlight %}


## FindAsync(Object[])
* DbSet.FindAsync Method
  - Asynchronously finds an entity with the given primary key values. 
  - If an entity with the given primary key values exists in the context, then it is returned immediately without making a request to the store. 
  - Otherwise, a request is made to the store for an entity with the given primary key values and this entity, if found, is attached to the context and returned. 
  - If no entity is found in the context or the store, then null is returned.
{% highlight C# %} 
  public async Task<IActionResult> OnGetAsync(int? id)
  {
    ...
    Product = await _context.Products.FindAsync(id);
    ...
  }
{% endhighlight %}


[Download](https://github.com/leehuhlee/CShap){: .btn}
