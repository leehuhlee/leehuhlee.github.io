---
layout: post
title: "[.Net 6] Blazor Ecommerce"
date: 2022-06-08
excerpt: "Blazor"
tags: [C#, .Net6, Blazor, WSAM]
comments: false
---

# Building a Walking Skeleton

## Create Project
  - Click `Blazor WebAssembly`
  - Click `ASP.NET Core hosted`. It will create Client, Server and Shared Projects.

<figure class="half">
  <a href="/assets/img/posts/blazor_ecommerce/0.jpg"><img src="/assets/img/posts/blazor_ecommerce/0.jpg"></a>
  <a href="/assets/img/posts/blazor_ecommerce/1.jpg"><img src="/assets/img/posts/blazor_ecommerce/1.jpg"></a>
  <figcaption>Create Project</figcaption>
</figure>

## Model

### Create Product
  - `[Column(TypeName = "decimal(18,2)")]` is for a decimal error on database. 
  - When you use the `decimal` type in your variable, you should set a scale of decimal.
  - Because the flotting point can make a misunderstand in memory.

* BlazorEcommerce.Shared/Product.cs
{% highlight cs %}
public class Product
{
    public int Id { get; set; }
    public string Title { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public string ImageUrl { get; set; } = string.Empty;
    [Column(TypeName = "decimal(18,2)")]
    public decimal Price { get; set; }
}
{% endhighlight %}
   
### Add Model on Client
  - To use a model from shared project, you should add `@using BlazorEcommerce.Shared` on _imports.razor

* BlazorEcommerce.Client/_imports.razor
{% highlight razor %}
...
@using BlazorEcommerce.Shared
{% endhighlight %}

## Database

### Packages in Server
  - Download `Microsoft.EntityFrameworkCore`, `Microsoft.EntityFrameworkCore.Design` and `Microsoft.EntityframeworkCore.Sqlserver` from Nuget Packages.
  - Install dotnet ef with a command `dotnet tool install --global dotnet-ef`.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/2.jpg"><img src="/assets/img/posts/blazor_ecommerce/2.jpg"></a>
  <figcaption>Packages in Server</figcaption>
</figure>

### Data Context

* BlazorEcommerce.Server/Data/DataContext.cs
{% highlight cs %}
public class DataContext : DbContext
{
    public DataContext(DbContextOptions<DataContext> options) : base(options){ }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Product>().HasData(
            new Product
            {
                Id = 1,
                Title = "The Hitchhiker's Guide to the Galaxy",
                Description = "The Hitchhiker's Guide to the Galaxy[note 1] (sometimes referred to as HG2G,[1] HHGTTG,[2] H2G2,[3] or tHGttG) is a comedy science fiction franchise created by Douglas Adams. Originally a 1978 radio comedy broadcast on BBC Radio 4, it was later adapted to other formats, including stage shows, novels, comic books, a 1981 TV series, a 1984 text-based computer game, and 2005 feature film.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/b/bd/H2G2_UK_front_cover.jpg",
                Price = 9.99m
            },
            new Product
            {
                Id = 2,
                Title = "Ready Player One",
                Description = "Ready Player One is a 2011 science fiction novel, and the debut novel of American author Ernest Cline. The story, set in a dystopia in 2045, follows protagonist Wade Watts on his search for an Easter egg in a worldwide virtual reality game, the discovery of which would lead him to inherit the game creator's fortune. Cline sold the rights to publish the novel in June 2010, in a bidding war to the Crown Publishing Group (a division of Random House).[1] The book was published on August 16, 2011.[2] An audiobook was released the same day; it was narrated by Wil Wheaton, who was mentioned briefly in one of the chapters.[3][4]Ch. 20 In 2012, the book received an Alex Award from the Young Adult Library Services Association division of the American Library Association[5] and won the 2011 Prometheus Award.[6]",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/a/a4/Ready_Player_One_cover.jpg",
                Price = 7.99m
            },
            new Product
            {
                Id = 3,
                Title = "Nineteen Eighty-Four",
                Description = "Nineteen Eighty-Four (also stylised as 1984) is a dystopian social science fiction novel and cautionary tale written by English writer George Orwell. It was published on 8 June 1949 by Secker & Warburg as Orwell's ninth and final book completed in his lifetime. Thematically, it centres on the consequences of totalitarianism, mass surveillance and repressive regimentation of people and behaviours within society.[2][3] Orwell, a democratic socialist, modelled the totalitarian government in the novel after Stalinist Russia and Nazi Germany.[2][3][4] More broadly, the novel examines the role of truth and facts within politics and the ways in which they are manipulated.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/c/c3/1984first.jpg",
                Price = 6.99m
            });
    }

    public DbSet<Product> Products { get; set; }
}
{% endhighlight %}

### Set Database

* BlazorEcommerce.Server/appsettings.json
{% highlight js %}
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=BlazorEcommerce;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False"
  },
  ...
{% endhighlight %}

  - Add information about database to server program.

* BlazorEcommerce.Server/Program.cs
{% highlight cs %}

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<DataContext>(options =>
{
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
}); 
...
{% endhighlight %}

### Update Database
  - In console, tpye `dotnet ef migrations add CreateInitial` and `dotnet ef database update`.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/3.jpg"><img src="/assets/img/posts/blazor_ecommerce/3.jpg"></a>
  <figcaption>Update Database</figcaption>
</figure>

## Controller
  - You can your own web Api on Controller.
  - Send and retuen data is json type.
  - When you create new controller, click `API Controller - Empty`.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/4.jpg"><img src="/assets/img/posts/blazor_ecommerce/4.jpg"></a>
  <figcaption>Update Database</figcaption>
</figure>

* BlazorEcommerce.Server/Controllers/ProductController.cs
{% highlight cs %}
[Route("api/[controller]")]
[ApiController]
public class ProductController : ControllerBase
{
    private readonly DataContext _context;

    public ProductController(DataContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<ActionResult<List<Product>>> GetProduct()
    {
        var products = await _context.Products.ToListAsync();
        return Ok(products);
    }
}
{% endhighlight %}

## Swagger

### Package in Server
  - Download `Swashbuckle.AspNetCore` from Nuget Packages.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/5.jpg"><img src="/assets/img/posts/blazor_ecommerce/5.jpg"></a>
  <figcaption>Package in Server</figcaption>
</figure>

### Set Swagger

* BlazorEcommerce.Server/Program.cs
{% highlight cs %}
...
builder.Services.AddRazorPages();

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
...
var app = builder.Build();

app.UseSwaggerUI();
app.UseSwagger();
...
{% endhighlight %}

### Run
  - Change Url to `.../swagger/index.html`.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/6.jpg"><img src="/assets/img/posts/blazor_ecommerce/6.jpg"></a>
  <figcaption>Run</figcaption>
</figure>

## Show Products
  
### Create ProductList Component
  - Razor file is for view.
* BlazorEcommerce.Client/Shared/ProductList.razor
{% highlight razor %}

<ul class="list-unstyled">
    @foreach(var product in Products)
    {
        <li class="media my-3">
            <div class="media-img-wrapper mr-2">
                <a href="#">
                    <img class="media-img" src="@product.ImageUrl" alt="@product.Title"/>
                </a>
            </div>
            <div class="media-body">
                <a href="#">
                    <h4 class="mb-0">@product.Title</h4>
                </a>
                <p>@product.Description</p>
                <h5 class="price">
                    $@product.Price
                </h5>
            </div>
        </li>
    }
</ul>

{% endhighlight %}

  - cs file is for code.
* BlazorEcommerce.Client/Shared/ProductList.razor.cs

{% highlight cs %}
public partial class ProductList
{
    [Inject]
    public HttpClient Http { get; set; }

    private static List<Product> Products = new List<Product>();

    protected override async Task OnInitializedAsync()
    {
        var result = await Http.GetFromJsonAsync<List<Product>>("api/product");
        if (result != null)
            Products = result;
    }
}
{% endhighlight %}

  - css file is for design.
* BlazorEcommerce.Client/Shared/ProductList.razor.css

{% highlight css %}
.media-img {
    max-height: 200px;
    max-width: 200px;
    border-radius: 6px;
    margin-bottom: 10px;
    transition: transform .2s;
}

    .media-img:hover {
        transform: scale(1.1);
    }

.media {
    display: flex;
    align-items: flex-start;
}

.media-body{
    flex: 1;
}

.media-img-wrapper {
    width: 200px;
    text-align: center;
}

.price {
    color: green;
}

@media (max-width:1023.98px) {
    .media {
        flex-direction: column;
    }

    .media-img-wrapper{
        width: 100%;
        height: auto;
    }
}
{% endhighlight %}

### Add ProductList Component on Index

* BlazorEcommerce.Client/Shared/ProductList.razor
{% highlight razor %}

@page "/"

<PageTitle>My Shop</PageTitle>

<ProductList/>

{% endhighlight %}

### Run

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/7.jpg"><img src="/assets/img/posts/blazor_ecommerce/7.jpg"></a>
  <figcaption>Run</figcaption>
</figure>

## Naming Options
  - Click `Tools`-`Options`-`Text Editor`-`C#`-`Code Style`-`Naming`.
  - To create a new naming Style, click `Manage naming styles`.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/9.jpg"><img src="/assets/img/posts/blazor_ecommerce/9.jpg"></a>
  <a href="/assets/img/posts/blazor_ecommerce/8.jpg"><img src="/assets/img/posts/blazor_ecommerce/8.jpg"></a>
  <figcaption>Run</figcaption>
</figure>

# Code

[Download](https://github.com/leehuhlee/BlazorEcommerce){: .btn}