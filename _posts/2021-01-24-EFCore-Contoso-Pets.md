---
layout: post
title: "[Entity Framework Core] Contoso Pets"
date: 2021-01-24
excerpt: "Entity Famework Core"
tags: [C#, DotNet, Entity Framework Core]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/Entity-Framework-Core-101">Entity Framework Core 101</a></center>


## NuGet Packages
* Microsoft.EntityFrameworkCore.SqlServer
  - Microsoft SQL Server database provider for Entity Framework Core.
* Microsoft.EntityFrameworkCore.Tools
  - Entity Framework Core Tools for the NuGet Package Manager Console in Visual Studio.


## Design
<figure>
  <a href="/assets/img/posts/efcore_contosopets/5.jpg"><img src="/assets/img/posts/efcore_contosopets/5.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets</figcaption>
</figure>


## Code
* Models\Customer.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace ContosoPets.Models
  {
    public class Customer
    {
      public int Id { get; set; }
      public string FirstName { get; set; }
      public string LastName { get; set; }
      public string? Address { get; set; }
      public string? Phone { get; set; }
      public string Email { get; set; }

      public ICollection<Order> Orders { get; set; }
    }
  }
{% endhighlight %}

* Models\Product.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.ComponentModel.DataAnnotations;
  using System.ComponentModel.DataAnnotations.Schema;
  using System.Text;

  namespace ContosoPets.Models
  {
    public class Product
    {
      public int Id { get; set; }

      [Required]
      public string Name { get; set; }

      [Column(TypeName = "decimal(18, 2)")]
      public decimal Price { get; set; }
    }
  }
{% endhighlight %}

* Models\Order.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;

  namespace ContosoPets.Models
  {
    public class Order
    {
      public int Id { get; set; }
      public DateTime OrderPlaced { get; set; }
      public DateTime? OrderFulfilled { get; set; }
      public int CustomerId { get; set; }

      public Customer Customer { get; set; }
      public ICollection<ProductOrder> ProductOrders { get; set; }

    }
  }
{% endhighlight %}


* Models\ProductOrder.cs
{% highlight C# %}
  namespace ContosoPets.Models
  {
    public class ProductOrder
    {
      public int Id { get; set; }
      public int Quantity { get; set; }
      public int ProductId { get; set; }
      public int OrderId { get; set; }

      public Order Order { get; set; }
      public Product Product { get; set; }
    }
  }
{% endhighlight %}


* Data\ContosPetsContext.cs
{% highlight C# %}
  using ContosoPets.Models;
  using Microsoft.EntityFrameworkCore;
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace ContosoPets.Data
  {
    class ContosoPetsContext : DbContext
    {
      public DbSet<Customer> Customers { get; set; }
      public DbSet<Order> Orders { get; set; }
      public DbSet<Product> Products { get; set; }
      public DbSet<ProductOrder> ProductOrders { get; set; }

      protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
      {
        optionsBuilder.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;" +
                                    "Initial Catalog=ContosoPets;" +
                                    "Integrated Security=True;" +
                                    "Connect Timeout=30;" +
                                    "Encrypt=False;" +
                                    "TrustServerCertificate=False;" +
                                    "ApplicationIntent=ReadWrite;" +
                                    "MultiSubnetFailover=False");
      }
    }
  }
{% endhighlight %}

* Program.cs
{% highlight C# %}
  using ContosoPets.Data;
  using ContosoPets.Models;
  using System;
  using System.Linq;

  namespace ContosoPets
  {
    class Program
    {
      static void Main(string[] args)
      {
        using ContosoPetsContext context = new ContosoPetsContext();

        // 1. Add Products //////////////////////////////
        Product squeakyBone = new Product()
        {
          Name = "Squeaky Dog Bone",
          Price = 4.99M
        };
        context.Products.Add(squeakyBone);

        Product tennisBalls = new Product()
        {
          Name = "Tennis Ball 3-Pack",
          Price = 9.99M
        };
        context.Add(tennisBalls);

        context.SaveChanges();
        /////////////////////////////////////////////////

        // 3-1. Modify Database /////////////////////////
        var squeakyBone = context.Products
                                .Where(p => p.Name == "Squeaky Dog Bone")
                                .FirstOrDefault();
        
        if(squeakyBone is Product)
        {
          squeakyBone.Price = 7.99m;
          // 3-2. Remove Entity /////////////////////////
          context.Remove(squeakyBone);
          ///////////////////////////////////////////////
        }
        context.SaveChanges();

        // 2-1. Query with Fluent API Syntex ////////////
        var products = context.Products
                      .Where(p => p.Price >= 5.00m)
                      .OrderBy(p => p.Name);
        /////////////////////////////////////////////////
        
        // 2-2. Query with Link Syntax //////////////////
        var products = from product in context.Products
                       where product.Price > 5.00m
                       orderby product.Name
                       select product;
        /////////////////////////////////////////////////

        // 1. Print ///////////////////////////////////
        foreach(Product p in products)
        {
          Console.WriteLine($"Id:     {p.Id}");
          Console.WriteLine($"Name:   {p.Name}");
          Console.WriteLine($"Price:  {p.Price}");
          Console.WriteLine(new string('-',20));
        }
        /////////////////////////////////////////////////
      }
    }
  }
{% endhighlight %}

## Package Manager Console
* Add-Migration [Name]
  - Creates a new migration class as per specified name with the `Up()` and `Down()` methods.
{% highlight C# %}
  Add-Migration AddEmail
{% endhighlight %}
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets/6.jpg"><img src="/assets/img/posts/efcore_contosopets/6.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets/10.jpg"><img src="/assets/img/posts/efcore_contosopets/10.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets/7.jpg"><img src="/assets/img/posts/efcore_contosopets/7.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets</figcaption>
</figure>

* Update-Database
  - Executes the last migration file created by the `Add-Migration` command and applies changes to the database schema.
{% highlight C# %}
  Update-Database
{% endhighlight %} 
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets/8.jpg"><img src="/assets/img/posts/efcore_contosopets/8.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets/11.jpg"><img src="/assets/img/posts/efcore_contosopets/11.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets/9.jpg"><img src="/assets/img/posts/efcore_contosopets/9.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets</figcaption>
</figure>


## String?
  - nullable
{% highlight C# %}
  public string? Address { get; set; }
  public string? Phone { get; set; }
{% endhighlight %} 


## ICollection<T>
  - Defines methods to manipulate generic collections.
  - ICollection is a interface that represents a collection, it also contains strongly typed members
{% highlight C# %}
  public ICollection<Order> Orders { get; set; }
{% endhighlight %}


## DataAnnotations
  - DataAnnotations are used in MVC to validate the data. 
  - It works on Client-side as well as server side. 
  - There are some pre-defined data-annotation attributes which we use directly to validate the data
* [Required]
  - the property should be not null or the property should not be a string type, which is either empty or whitespace
{% highlight C# %}
  [Required]
  public string Name { get; set; }
{% endhighlight %}
* [Column(TypeName = "[Datatype Condition]"]
  - Condition of Column
{% highlight C# %}
  [Column(TypeName = "decimal(18, 2)")]
  public decimal Price { get; set; }
{% endhighlight %}


## DbContext
* DbContext
  - DbContext is called context class in entity framework.
  - DbContext is the primary class that is responsible for interacting with the database<br>
    Querying: Converts LINQ-to-Entities queries to SQL query and sends them to the database.<br>
    Change Tracking: Keeps track of changes that occurred on the entities after querying from the database.<br>
    Persisting Data: Performs the Insert, Update and Delete operations to the database, based on entity states.<br>
    Caching: Provides first level caching by default. It stores the entities which have been retrieved during the life time of a context class.<br>
    Manage Relationship: Manages relationships using CSDL, MSL and SSDL in Db-First or Model-First approach, and using fluent API configurations in Code-First approach.<br>
    Object Materialization: Converts raw data from the database into entity objects.<br>
{% highlight C# %}
  class ContosoPetsContext : DbContext
{% endhighlight %}
* DbSet
   - The `DbSet` class represents an entity set that can be used for create, read, update, and delete operations.
   - The `DbContext` must include the DbSet type properties for the entities which map to database tables and views.
{% highlight C# %}
  public DbSet<Customer> Customers { get; set; }
  public DbSet<Order> Orders { get; set; }
  public DbSet<Product> Products { get; set; }
  public DbSet<ProductOrder> ProductOrders { get; set; }
{% endhighlight %}


## Override
  - The override modifier is required to extend or modify the abstract or virtual implementation of an inherited method, property, indexer, or event.
  - An override method provides a new implementation of the method inherited from a base class.
  - The method that is overridden by an override declaration is known as the overridden base method.
  - An override method must have the same signature as the overridden base method. 
{% highlight C# %}
  protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{% endhighlight %}


## DbContextOptionsBuilder
  - Provides a simple API surface for configuring DbContextOptions. 
  - Databases (and other extensions) typically define extension methods on this object that allow you to configure the database connection (and other options) to be used for a context.
  - You can use DbContextOptionsBuilder to configure a context by overriding OnConfiguring(DbContextOptionsBuilder) or creating a DbContextOptions externally and passing it to the context constructor.
{% highlight C# %}
  protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{% endhighlight %}


## UseSqlServer
* UseSqlServer
  - Configures the context to connect to a Microsoft SQL Server database, but without initially setting any DbConnection or connection string.
  - The connection or connection string must be set before the DbContext is used to connect to a database. 
{% highlight C# %}
  optionsBuilder.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;" +
                                        "Initial Catalog=ContosoPets;" +
                                        "Integrated Security=True;" +
                                        "Connect Timeout=30;" +
                                        "Encrypt=False;" +
                                        "TrustServerCertificate=False;" +
                                        "ApplicationIntent=ReadWrite;" +
                                        "MultiSubnetFailover=False");
{% endhighlight %}


## Connection String Syntax
* Data Source
  - To connect to a named instance of SQL Server, use the `server name\instance` name syntax.
  - You can also set the DataSource property of the `SqlConnectionStringBuilder` to the instance name when building a connection string. 
  - The `DataSource` property of a `SqlConnection` object is read-only.
{% highlight C# %}
  "Data Source=(localdb)\\MSSQLLocalDB;"
{% endhighlight %}
* Initial Catalog
  - If the user name that is in the connection string has access to more then one database, you have to specify the database you want the connection string to connect to.
{% highlight C# %}
  "Initial Catalog=ContosoPets;"
{% endhighlight %}
* Intergrated Security
  - When `false`, User ID and Password are specified in the connection. 
  - When `true`, the current Windows account credentials are used for authentication.
{% highlight C# %}
  "Integrated Security=True;"
{% endhighlight %}
* Connect Timeout
  - The length of time (in seconds) to wait for a connection to the server before terminating the attempt and generating an error.
{% highlight C# %}
  "Connect Timeout=30;"
{% endhighlight %}
* Encrypt
  - When true, SQL Server uses SSL encryption for all data sent between the client and server if the server has a certificate installed. 
{% highlight C# %}
  "Encrypt=False;"
{% endhighlight %}
* TrustServerCertificate
  - The `TrustServerCertificate` keyword is valid only when connecting to a `SQL Server instance` with a valid certificate. 
  - When `TrustServerCertificate` is set to `true`, the transport layer will use `SSL` to encrypt the channel and bypass walking the certificate chain to validate trust.
{% highlight C# %}
  "TrustServerCertificate=False;"
{% endhighlight %}
* ApplicationIntent
  - The keyword ApplicationIntent can be specified in your connection string. The assignable values are ReadWrite or ReadOnly. The default is ReadWrite.
  - When ApplicationIntent=ReadOnly, the client requests a read workload when connecting. The server enforces the intent at connection time, and during a USE database statement.
  - The ApplicationIntent keyword does not work with legacy read-only databases.
{% highlight C# %}
  "ApplicationIntent=ReadWrite;"
{% endhighlight %}
* MultiSubnetFailover
  - Always specify `MultiSubnetFailover=Yes` when connecting to a SQL Server 2012 availability group listener or SQL Server 2012 Failover Cluster Instance. MultiSubnetFailover enables faster failover for all Availability Groups and failover cluster instance in SQL Server 2012 and will significantly reduce failover time for single and multi-subnet Always On topologies. During a multi-subnet failover, the client will attempt connections in parallel. During a subnet failover, SQL Server Native Client will aggressively retry the TCP connection.
  - The MultiSubnetFailover connection property indicates that the application is being deployed in an availability group or Failover Cluster Instance, and that SQL Server Native Client will try to connect to the database on the primary SQL Server instance by trying to connect to all the IP addresses. When `MultiSubnetFailover=Yes` is specified for a connection, the client retries TCP connection attempts faster than the operating system's default TCP retransmit intervals. This enables faster reconnection after failover of either an Always On Availability Group or an Always On Failover Cluster Instance, and is applicable to both single- and multi-subnet Availability Groups and Failover Cluster Instances.
{% highlight C# %}
  "MultiSubnetFailover=False"
{% endhighlight %}


## Result
<figure class="half">
  <a href="/assets/img/posts/efcore_contosopets/0.jpg"><img src="/assets/img/posts/efcore_contosopets/0.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets/1.jpg"><img src="/assets/img/posts/efcore_contosopets/1.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets 1 & 2</figcaption>
</figure>
<figure class="half">
  <a href="/assets/img/posts/efcore_contosopets/2.jpg"><img src="/assets/img/posts/efcore_contosopets/2.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets/3.jpg"><img src="/assets/img/posts/efcore_contosopets/3.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets 3-1</figcaption>
</figure>
<figure>
  <a href="/assets/img/posts/efcore_contosopets/4.jpg"><img src="/assets/img/posts/efcore_contosopets/4.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets 3-2</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
