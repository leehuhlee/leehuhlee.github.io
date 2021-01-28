---
layout: post
title: "[Entity Framework Core] Contoso Pets UI"
date: 2021-01-26
excerpt: "Entity Famework Core and ASP.NET"
tags: [C#, DotNet, Entity Framework Core, ASPDotNet]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/Entity-Framework-Core-101">Entity Framework Core 101</a></center>


## NuGet Packages
* Microsoft.EntityFrameworkCore.Sqlite
 - SQLite database provider for Entity Framework Core.
* Microsoft.Extensions.Logging.Debug
 - Debug output logger provider implementation for Microsoft.Extensions.Logging. 
 - This logger logs messages to a debugger monitor by writing messages with System.Diagnostics.Debug.WriteLine().
* Microsoft.VisualStudio.Web.CodeGeneration.Design
 - Code Generation tool for ASP.NET Core. Contains the dotnet-aspnet-codegenerator command used for generating controllers and views.
<figure>
  <a href="/assets/img/posts/efcore_contosopets_ui/0.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/0.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>

# Models

## Code
* Customer.cs
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

      public ICollection<Order> Orders { get; set; }
    }
  }
{% endhighlight %}

* Order.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  namespace ContosoPets.Ui.Models
  {

    public class Order
    {
      public Order()
      {
        ProductOrders = new HashSet<ProductOrder>();
      }

      public int Id { get; set; }
      public DateTime OrderPlaced { get; set; }
      public DateTime? OrderFulfilled { get; set; }
      public int CustomerId { get; set; }

      public Customer Customer { get; set; }
      public ICollection<ProductOrder> ProductOrders { get; set; }
    }
  }
{% endhighlight %}

* Product.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.ComponentModel.DataAnnotations;
  using System.ComponentModel.DataAnnotations.Schema;
  using System.Linq;
  using System.Threading.Tasks;

  namespace ContosoPets.Ui.Models
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

* ProductOrder.cs
{% highlight C# %}
  namespace ContosoPets.Ui.Models
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

## HashSet
* HashSet
  - In C#, HashSet is an unordered collection of unique elements. 
  - It supports the implementation of sets and uses the hash table for storage. 
  - This collection is of the generic type collection and it is defined under System.Collections.Generic namespace. 
  - It is generally used when we want to prevent duplicate elements from being placed in the collection. The performance of the HashSet is much better in comparison to the list.
{% highlight C# %}
  Orders = new HashSet<Order>();
{% endhighlight %}


# Pages

## Razor
* Razor
  - Razor is a markup syntax for embedding server-based code into webpages. 
  - The Razor syntax consists of Razor markup, C#, and HTML. 
  - Files containing Razor generally have a .cshtml file extension.
  - Razor is also found in Razor components files (.razor).
* Razor HTML
  - The default Razor language is HTML. 
  - Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.
  - HTML markup in .cshtml Razor files is rendered by the server unchanged.
* Razor Syntax
  - Razor supports C# and uses the `@` symbol to transition from HTML to C#. 
  - Razor evaluates C# expressions and renders them in the HTML output.
  - When an `@` symbol is followed by a Razor reserved keyword, it transitions into Razor-specific markup. 
  - Otherwise, it transitions into plain C#.

## Code
* Error.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Diagnostics;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.Extensions.Logging;

  namespace ContosoPets.Ui.Pages
  {
    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
    public class ErrorModel : PageModel
    {
      public string RequestId { get; set; }

      public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

      private readonly ILogger<ErrorModel> _logger;

      public ErrorModel(ILogger<ErrorModel> logger)
      {
        _logger = logger;
      }

      public void OnGet()
      {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
      }
    }
  }
{% endhighlight %}

* Index.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;  
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.Extensions.Logging;

  namespace ContosoPets.Ui.Pages
  {
    public class IndexModel : PageModel
    {
      private readonly ILogger<IndexModel> _logger;

      public IndexModel(ILogger<IndexModel> logger)
      {
        _logger = logger;
      }

      public void OnGet(){  }
    }
  }
{% endhighlight %}

* Privacy.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.Extensions.Logging;

  namespace ContosoPets.Ui.Pages
  {
    public class PrivacyModel : PageModel
    {
      private readonly ILogger<PrivacyModel> _logger;

      public PrivacyModel(ILogger<PrivacyModel> logger)
      {
        _logger = logger;
      }

      public void OnGet(){  }
    }
  }
{% endhighlight %}


## [ResponseCache(Condition)]
* ResponseCache
  - Response caching reduces the number of requests a client or proxy makes to a web server. 
  - Response caching also reduces the amount of work the web server performs to generate a response.
  - Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.
* Duration
  - To enable caching, Duration must be set to a positive value
* Location
  - Location's options of Any and Client translate into Cache-Control header values of public and private, respectively. 
  - As noted in the NoStore and Location.None section, setting Location to None sets both Cache-Control and Pragma headers to no-cache.
* NoStore
  - A cache must not store the request.
  - A cache must not store any part of the response.
{% highlight C# %}
  [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
{% endhighlight %}


## PageModel
* PageModel Class
  - Abstract class representing a Page
  - Razor Pages are derived from `PageModel`. 
  - On a Razor Page, OnGetAsync or OnGet is called to initialize the state of the page.
{% highlight C# %}
  public class ErrorModel : PageModel{...}
{% endhighlight %}


## =>
  - The => token is supported in two forms: as the lambda operator and as a separator of a member name and the member implementation in an expression body definition.
* Lamda Expression
  - In lambda expressions, the lambda operator => separates the input parameters on the left side from the lambda body on the right side.
* Expression body definition
  - An expression body definition has the following general syntax: member => expression; where expression is a valid expression. 
  - The return type of expression must be implicitly convertible to the member's return type. 
  - If the member's return type is void or if the member is a constructor, a finalizer, or a property or indexer set accessor, expression must be a statement expression. 
  - Because the expression's result is discarded, the return type of that expression can be any type.
{% highlight C# %}
  public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
{% endhighlight %}

## String.IsNullOrEmpty(String)
* String.IsNullOrEmpty(String) Method
  - Indicates whether the specified string is null or an empty string ("").
{% highlight C# %}
  public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
{% endhighlight %}


## readonly
* readonly
  - The `readonly` keyword is a modifier
  - In a field declaration, `readonly` indicates that assignment to the field can only occur as part of the declaration or in a constructor in the same class. A `readonly` field can be assigned and reassigned multiple times within the field declaration and constructor.
  - In a `readonly` struct type definition, `readonly` indicates that the structure type is immutable.
  - In an instance member declaration within a structure type, `readonly` indicates that an instance member doesn't modify the state of the structure.
  - In a ref `readonly` method return, the `readonly` modifier indicates that method returns a reference and writes aren't allowed to that reference.
{% highlight C# %}
  private readonly ILogger<ErrorModel> _logger;
{% endhighlight %}


## ILogger
* ILogger Interface
  - Represents a type used to perform logging.
{% highlight C# %}
  private readonly ILogger<ErrorModel> _logger;
{% endhighlight %}


## ??
* ?? Operator
  - The null-coalescing operator ?? returns the value of its left-hand operand if it isn't null; 
  - Otherwise, it evaluates the right-hand operand and returns its result. The ?? operator doesn't evaluate its right-hand operand if the left-hand operand evaluates to non-null.
{% highlight C# %}
  RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
{% endhighlight %}


## Activity
* Activity.Current Property
  - Gets or sets the current operation (Activity) for the current thread. 
  - This flows across async calls.
* Activity.Id Property
  - Gets an identifier that is specific to a particular request.
{% highlight C# %}
  RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
{% endhighlight %}


## HttpContext.TraceIdentifier
* HttpContext.TraceIdentifier Property
  - Gets or sets a unique identifier to represent this request in trace logs.
{% highlight C# %}
  RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
{% endhighlight %}


# Pages\Products
  - Codes are made from Razor.

## How to make Razor pages
* Make New Folder
  - This folder will be named URL address.
* Add Razor Page
  - Right click the folder - [Add] - [Razor Page]
  - Click `Razor Page using Entity Framework(CRUD)` - Set `Model Class` and `Data Context Class`
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets_ui/13.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/13.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/14.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/14.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/15.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/15.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>

* Check your Folder
<figure class="half">
  <a href="/assets/img/posts/efcore_contosopets_ui/3.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/3.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/2.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/2.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>


## Code
* Create.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.AspNetCore.Mvc.Rendering;
  using ContosoPets.Ui.Data;
  using ContosoPets.Ui.Models;

  namespace ContosoPets.Ui.Pages.Products
  {
    public class CreateModel : PageModel
    {
      private readonly ContosoPets.Ui.Data.ContosoPetsContext _context;

      public CreateModel(ContosoPets.Ui.Data.ContosoPetsContext context)
      {
        _context = context;
      }

      public IActionResult OnGet()
      {
        return Page();
      }

      [BindProperty]
      public Product Product { get; set; }

      // To protect from overposting attacks, see https://aka.ms/RazorPagesCRUD
      public async Task<IActionResult> OnPostAsync()
      {
        if (!ModelState.IsValid)
        {
          return Page();
        }

         _context.Products.Add(Product);
        await _context.SaveChangesAsync();

        return RedirectToPage("./Index");
      }
    }
  }
{% endhighlight %}

* Delete.cshtml.cs
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

        Product = await _context.Products.FirstOrDefaultAsync(m => m.Id == id);

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

* Details.cshtml.cs
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
    public class DetailsModel : PageModel
    {
      private readonly ContosoPets.Ui.Data.ContosoPetsContext _context;

      public DetailsModel(ContosoPets.Ui.Data.ContosoPetsContext context)
      {
         _context = context;
      }

      public Product Product { get; set; }

      public async Task<IActionResult> OnGetAsync(int? id)
      {
        if (id == null)
        {
          return NotFound();
        }

        Product = await _context.Products.FirstOrDefaultAsync(m => m.Id == id);

        if (Product == null)
        {
          return NotFound();
        }
        return Page();
      }
    }
  }
{% endhighlight %}

* Edit.cshtml.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Mvc;
  using Microsoft.AspNetCore.Mvc.RazorPages;
  using Microsoft.AspNetCore.Mvc.Rendering;
  using Microsoft.EntityFrameworkCore;
  using ContosoPets.Ui.Data;
  using ContosoPets.Ui.Models;

  namespace ContosoPets.Ui.Pages.Products
  {
    public class EditModel : PageModel
    {
      private readonly ContosoPets.Ui.Data.ContosoPetsContext _context;

      public EditModel(ContosoPets.Ui.Data.ContosoPetsContext context)
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

        Product = await _context.Products.FirstOrDefaultAsync(m => m.Id == id);

        if (Product == null)
        {
          return NotFound();
        }
        return Page();
      }

      // To protect from overposting attacks, enable the specific properties you want to bind to.
      // For more details, see https://aka.ms/RazorPagesCRUD.
      public async Task<IActionResult> OnPostAsync()
      {
        if (!ModelState.IsValid)
        {
          return Page();
        }

        _context.Attach(Product).State = EntityState.Modified;

        try
        {
          await _context.SaveChangesAsync();
        }
        catch (DbUpdateConcurrencyException)
        {
          if (!ProductExists(Product.Id))
          {
            return NotFound();
          }
          else
          {
            throw;
          }
        }

        return RedirectToPage("./Index");
      }

      private bool ProductExists(int id)
      {
        return _context.Products.Any(e => e.Id == id);
      }
    }
  }
{% endhighlight %}

* Index.cshtml.cs
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
    public class IndexModel : PageModel
    {
      private readonly ContosoPets.Ui.Data.ContosoPetsContext _context;

      public IndexModel(ContosoPets.Ui.Data.ContosoPetsContext context)
      {
        _context = context;
      }

      public IList<Product> Product { get;set; }

      public async Task OnGetAsync()
      {
        Product = await _context.Products.ToListAsync();
      }
    }
  }
{% endhighlight %}


## IActionResult
* IActionResult
  - Defines a contract that represents the result of an action method.
{% highlight C# %}
  public IActionResult OnGet(){...}
{% endhighlight %}


## OnGet() and OnPostAsync()
* Handler Methods in Razor Pages
  - Handler methods in Razor Pages are methods that are automatically executed as a result of a request. The Razor Pages framework uses a naming convention to select the appropriate handler method to execute. The default convention works by matching the HTTP verb used for the request to the name of the method, which is prefixed with "On": OnGet(), OnPost(), OnPut() etc.
  - Handler methods also have optional asynchronous equivalents: OnPostAsync(), OnGetAsync() etc.
* OnPostAsync()
  - The page has an OnPostAsync handler method, which runs on POST requests (when a user posts the form). 
  - Check for validation errors.
  - If there are no errors, save the data and redirect.
  - If there are errors, show the page again with validation messages. In many cases, validation errors would be detected on the client, and never submitted to the server.
* OnGet()
  - OnGet to initialize state needed for the page. 
  - In the preceding code, the OnGet method displays the CreateModel.cshtml Razor Page.
* OnPost()
  - OnPost to handle form submissions.
{% highlight C# %}
  public IActionResult OnGet(){...}
  ...
  public async Task<IActionResult> OnPostAsync(){...}
{% endhighlight %}


## Page() 
* Page Constructor
  - Initializes a new instance of the Page class.
{% highlight C# %}
  public IActionResult OnGet()
  {
    return Page();
  }
{% endhighlight %}


## [BindProperty]
* Model Binding
  - Controllers and Razor pages work with data that comes from HTTP requests.
  - Model binding automates this process.<br>
    Retrieves data from various sources such as route data, form fields, and query strings.<br>
    Provides the data to controllers and Razor pages in method parameters and public properties.<br>
    Converts string data to .NET types.<br>
    Updates properties of complex types.
* [BindProperty] attribute
  - Can be applied to a public property of a controller or PageModel class to cause model binding to target that property
{% highlight C# %}
  [BindProperty]
  public Product Product { get; set; }
{% endhighlight %}


## Async
* Asynchronous programming with async and await
  - The `Task asynchronous programming model` (TAP) provides an abstraction over asynchronous code.
  - In many scenarios, you want to start several independent tasks immediately. 
  - Then, as each task finishes, you can continue other work that's ready.
* async
  - That signals to the compiler that this method contains an await statement; it contains asynchronous operations.
  - This method returns a Task<TResult> that represents the composition of those three operations.
* Task
  - The System.Threading.Tasks.Task and related types are classes you can use to reason about tasks that are in progress.
  - You start a task and hold on to the Task object that represents the work. You'll `await` each task before working with its result.
* await
  - The await keyword provides a non-blocking way to start a task, then continue execution when that task completes. 
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    ...
    await _context.SaveChangesAsync();
    ...
  }
{% endhighlight %}


## ModelState
* ModelState Property
  - a property of a `Controller` object, and can be accessed from those classes that inherit from System.Web.Mvc.Controller.
  - The ModelState represents a collection of name and value pairs that were submitted to the server during a POST.
  - to store the value submitted to the server, and to store the validation errors associated with those values.
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    if (!ModelState.IsValid){...}
    ...
  }
{% endhighlight %}


## DbSet Methods
* Add
  - Adds the given entity to the context underlying the set in the Added state such that it will be inserted into the database when SaveChanges is called.
* Delete
  - Marks the given entity as Deleted such that it will be deleted from the database when SaveChanges is called. Note that the entity must exist in the context in some other state before this method is called.
* Attach
  - Attaches the given entity to the context underlying the set. That is, the entity is placed into the context in the Unchanged state, just as if it had been read from the database.
  - When you do `context.Entry(entity).State = EntityState.Modified;`, you are not only attaching the entity to the DbContext, you are also marking the whole entity as dirty. This means that when you do context.SaveChanges(), EF will generate an update statement that will update all the fields of the entity.
* Any
  - Your call to Any() can be translated into a SQL query which returns a boolean
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    ...
    _context.Products.Add(Product);
    ...
  }

  public async Task<IActionResult> OnPostAsync(int? id)
  {
    ...
    if (...)
    {
      _context.Products.Remove(Product);
      ...
    }
    ...
  }

  public async Task<IActionResult> OnPostAsync()
  {
    ...
    _context.Attach(Product).State = EntityState.Modified;
    ...
  }

  private bool ProductExists(int id)
  {
    return _context.Products.Any(e => e.Id == id);
  }
{% endhighlight %}


## SaveChangesAsync()
* DbContext.SaveChangesAsync Method
  - Asynchronously saves all changes made in this context to the underlying database.
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    ...
    await _context.SaveChangesAsync();
    ...
  }
{% endhighlight %}


## RedirectToPage(String)
* PageBase.RedirectToPage Method
  - Redirects to the specified pageName.
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    ...
    return RedirectToPage("./Index");
  }
{% endhighlight %}


## NotFound()
* ControllerBase.NotFound Method
  - Creates an `NotFoundResult` that produces a `Status404NotFound` response.
{% highlight C# %}
  public async Task<IActionResult> OnGetAsync(int? id)
  {
    ...
    if(...)
    {
      return NotFound();
    }
    ...
  }
{% endhighlight %}


## FirstOrDefaultAsync(...)
* QueryableExtensions.FirstOrDefaultAsync Method
  - Asynchronously returns the first element of a sequence, or a default value if the sequence contains no elements.
{% highlight C# %}
  public async Task<IActionResult> OnGetAsync(int? id){
    ...
    Product = await _context.Products.FirstOrDefaultAsync(m => m.Id == id);
    ...
  }
{% endhighlight %}


## FindAsync(...)
* DbSet.FindAsync Method
  - Asynchronously finds an entity with the given primary key values. If an entity with the given primary key values exists in the context, then it is returned immediately without making a request to the store. Otherwise, a request is made to the store for an entity with the given primary key values and this entity, if found, is attached to the context and returned. If no entity is found in the context or the store, then null is returned.
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync(int? id)
  {
    ...
    Product = await _context.Products.FindAsync(id);
    ...
  }
{% endhighlight %}


## EntityState
* EntityState Enum
  - Added	4<br>
  The object is new, has been added to the object context, and the SaveChanges() method has not been called. After the changes are saved, the object state changes to Unchanged. Objects in the Added state do not have original values in the ObjectStateEntry.
  - Deleted	8<br> 
  The object has been deleted from the object context. After the changes are saved, the object state changes to Detached.
  - Detached	1<br>
  The object exists but is not being tracked. An entity is in this state immediately after it has been created and before it is added to the object context. An entity is also in this state after it has been removed from the context by calling the Detach(Object) method or if it is loaded by using a NoTrackingMergeOption. There is no ObjectStateEntry instance associated with objects in the Detached state.
  - Modified	16<br>
  One of the scalar properties on the object was modified and the SaveChanges() method has not been called. In POCO entities without change-tracking proxies, the state of the modified properties changes to Modified when the DetectChanges() method is called. After the changes are saved, the object state changes to Unchanged.
  - Unchanged	2<br>
  The object has not been modified since it was attached to the context or since the last time that the SaveChanges() method was called.
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    ...
    _context.Attach(Product).State = EntityState.Modified;
    ...
  }
{% endhighlight %}


## DbUpdateConcurrencyException
* DbUpdateConcurrencyException Class
  - Exception thrown by DbContext when it was expected that SaveChanges for an entity would result in a database update but in fact no rows in the database were affected. This usually indicates that the database has been concurrently updated such that a concurrency token that was expected to match did not actually match. Note that state entries referenced by this exception are not serialized due to security and accesses to the state entries after serialization will return null.
{% highlight C# %}
  public async Task<IActionResult> OnPostAsync()
  {
    try
    {
      ...
    }
    catch (DbUpdateConcurrencyException)
    {
      ...
    }
  }
{% endhighlight %}


## ToListAsync()
* Async LINQ operators
  - In order to support executing LINQ queries asynchronously, EF Core provides a set of async extension methods which execute the query and return results. These counterparts to the standard, synchronous LINQ operators include ToListAsync, SingleAsync, AsAsyncEnumerable, etc.
  - `ToListAsync()` is `ToList` asynchronous. Asynchronous methods can be executed without crashing the application's main thread.
{% highlight C# %}
  public async Task OnGetAsync()
  {
    Product = await _context.Products.ToListAsync();
  }
{% endhighlight %}


# .NET Generic Host in ASP.NET Core
  - The ASP.NET Core templates create a .NET Core Generic Host (HostBuilder).

## Code
* Program.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Hosting;
  using Microsoft.Extensions.Logging;

  namespace ContosoPets.Ui
  {
    public class Program
    {
      public static void Main(string[] args)
      {
        CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
          .ConfigureWebHostDefaults(webBuilder =>
          {
            webBuilder.UseStartup<Startup>();
          });
    }
  }
{% endhighlight %}

* Startup.cs
{% highlight C# %}
  using ContosoPets.Ui.Data;
  using Microsoft.AspNetCore.Builder;
  using Microsoft.AspNetCore.Hosting;
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
          options.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0"));
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


## Host
  - A host is an object that encapsulates an app's resources
* Dependency injection (DI)
* Logging
* Configuration
* IHostedService implementations


## Set up a host
  - The host is typically configured, built, and run by code in the Program class.
* Main Method
  - Calls a CreateHostBuilder method to create and configure a builder object.
  - Calls Build and Run methods on the builder object.
* HTTP workload
  - CreateHostBuilder calls ConfigureWebHostDefaults

## Build().Run()
* IHostBuilder.Build Method
  - Run the given actions to initialize the host. 
  - This can only be called once.
* Host.Run Method
  - The Run method starts the web app and blocks the calling thread until the host is shut down
{% highlight C# %}
  public static void Main(string[] args)
  {
    CreateHostBuilder(args).Build().Run();
  }
{% endhighlight %}


## CreateDefaultBuilder 
* CreateDefaultBuilder Method
  - Sets the content root to the path returned by GetCurrentDirectory.
  - Loads host configuration from:<br>
    Environment variables prefixed with DOTNET_.<br>
    Command-line arguments.
  - Loads app configuration from:<br>
    appsettings.json.<br>
    appsettings.{Environment}.json.<br>
    User secrets when the app runs in the Development environment.<br>
    Environment variables.<br>
    Command-line arguments.
  - Adds the following logging providers:<br>
    Console
    Debug
    EventSource
    EventLog (only when running on Windows)
  - Enables scope validation and dependency validation when the environment is Development.


## ConfigureWebHostDefaults 
* ConfigureWebHostDefaults Method
  - Loads host configuration from environment variables prefixed with ASPNETCORE_.
  - Sets Kestrel server as the web server and configures it using the app's hosting configuration providers. For the Kestrel server's default options, see Configure options for the ASP.NET Core Kestrel web server.
  - Adds Host Filtering middleware.
  - Adds Forwarded Headers middleware if ASPNETCORE_FORWARDEDHEADERS_ENABLED equals true.
  - Enables IIS integration. For the IIS default options, see Host ASP.NET Core on Windows with IIS.


## StartupAssembly
* StartupAssembly
  - The assembly to search for the Startup class.
  - To set this value, use the environment variable or call UseStartup. 
  - UseStartup can take an assembly name (string) or a type (TStartup). If multiple UseStartup methods are called, the last one takes precedence.

{% highlight C# %}
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
      .ConfigureWebHostDefaults(webBuilder =>
      {
        webBuilder.UseStartup<Startup>();
      });
{% endhighlight %}


## Razor Pages
  - Razor Pages is enabled in Startup.cs


## IServiceCollection
* IServiceCollection Interface
  - Specifies the contract for a collection of service descriptors.

## AddRazorPages()
* MvcServiceCollectionExtensions.AddRazorPages Method
  - Adds services for pages to the specified IServiceCollection.


## AddDbContext()
* EntityFrameworkServiceCollectionExtensions.AddDbContext Method
  - Registers the given context as a service in the IServiceCollection.
  - Use this method when using dependency injection in your application, such as with ASP.NET Core. 
  - For applications that don't use dependency injection, consider creating DbContext instances directly with its constructor. 
  - The OnConfiguring(DbContextOptionsBuilder) method can then be overridden to configure a connection string and other options.
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddRazorPages();
    services.AddDbContext<ContosoPetsContext>(options =>
    options.UseSqlServer("Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ContosoPets;Integrated Security=True;ConnectRetryCount=0"));
  }
{% endhighlight %}


## IApplicationBuilder
* IApplicationBuilder Interface
  - Defines a class that provides the mechanisms to configure an application's request pipeline.


## IWebHostEnvironment
* IWebHostEnvironment Interface
  - Provides information about the web hosting environment an application is running in.


## IsDevelopment()
* HostingEnvironmentExtensions.IsDevelopment(IHostingEnvironment) Method
  - Checks if the current hosting environment name is Development.


## UseDeveloperExceptionPage()
* DeveloperExceptionPageExtensions.UseDeveloperExceptionPage Method
  - Captures synchronous and asynchronous Exception instances from the pipeline and generates HTML error responses.


## UseExceptionHandler(String)
* ExceptionHandlerExtensions.UseExceptionHandler Method
  - Adds a middleware to the pipeline that will catch exceptions, log them, reset the request path, and re-execute the request. 
  - The request will not be re-executed if the response has already started.


## UseHsts()
* HstsBuilderExtensions.UseHsts(IApplicationBuilder) Method
  - Adds middleware for using HSTS, which adds the Strict-Transport-Security header.


## UseHttpsRedirection()
* HttpsPolicyBuilderExtensions.UseHttpsRedirection(IApplicationBuilder) Method
  - Adds middleware for redirecting HTTP Requests to HTTPS.


## UseStaticFiles()
* StaticFileExtensions.UseStaticFiles Method
  - Enables static file serving for the current request path


## UseRouting()
* EndpointRoutingApplicationBuilderExtensions.UseRouting(IApplicationBuilder) Method
  - Adds a Microsoft.AspNetCore.Routing.EndpointRoutingMiddleware middleware to the specified IApplicationBuilder.


## UseAuthorization()
* AuthorizationAppBuilderExtensions.UseAuthorization(IApplicationBuilder) Method
  - Adds the AuthorizationMiddleware to the specified IApplicationBuilder, which enables authorization capabilities.


## UseEndpoints(Action)
* EndpointRoutingApplicationBuilderExtensions.UseEndpoints(IApplicationBuilder, Action<IEndpointRouteBuilder>) Method
  - Adds a Microsoft.AspNetCore.Routing.EndpointMiddleware middleware to the specified IApplicationBuilder with the EndpointDataSource instances built from configured IEndpointRouteBuilder. 
  - The Microsoft.AspNetCore.Routing.EndpointMiddleware will execute the Endpoint associated with the current request.


## MapRazorPages()
* RazorPagesEndpointRouteBuilderExtensions.MapRazorPages(IEndpointRouteBuilder) Method
  - Adds endpoints for Razor Pages to the IEndpointRouteBuilder.

{% highlight C# %}
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
{% endhighlight %}


# Result

## Model
<figure class="half">
  <a href="/assets/img/posts/efcore_contosopets_ui/5.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/5.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/4.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/4.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>


## Create
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets_ui/6.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/6.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/7.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/7.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/8.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/8.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>


## Edit
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets_ui/8.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/8.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/9.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/9.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/16.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/16.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>


## Delete
<figure class="third">
  <a href="/assets/img/posts/efcore_contosopets_ui/10.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/10.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/11.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/11.jpg"></a>
  <a href="/assets/img/posts/efcore_contosopets_ui/12.jpg"><img src="/assets/img/posts/efcore_contosopets_ui/12.jpg"></a>
	<figcaption>Entity Framework Core Contoso Pets UI</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
