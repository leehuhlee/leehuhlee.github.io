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

### Set

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

<figure class="half">
  <a href="/assets/img/posts/blazor_ecommerce/9.jpg"><img src="/assets/img/posts/blazor_ecommerce/9.jpg"></a>
  <a href="/assets/img/posts/blazor_ecommerce/8.jpg"><img src="/assets/img/posts/blazor_ecommerce/8.jpg"></a>
  <figcaption>Run</figcaption>
</figure>

# Products

## Models for Product Service

###  Models

* BlazorEcommerce.Shared/Product.cs
{% highlight cs %}
public class Product
{
    public int Id { get; set; }
    public string Title { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public string ImageUrl { get; set; } = string.Empty;
    [Column(TypeName = "decimal(18,2)")]
    public Category? Category { get; set; }
    public int CategoryId { get; set; }
    public bool Featured { get; set; } = false;
    public List<ProductVariant> Variants { get; set; } = new List<ProductVariant>();
}
{% endhighlight %}

* BlazorEcommerce.Shared/Category.cs
{% highlight cs %}
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Url { get; set; } = string.Empty;
}
{% endhighlight %}

* BlazorEcommerce.Shared/ProductVariant.cs
{% highlight cs %}
public class ProductVariant
{
    [JsonIgnore]
    public Product Product { get; set; }
    public int ProductId { get; set; }
    public ProductType ProductType { get; set; }
    public int ProductTypeId { get; set; }
    [Column(TypeName="decimal(18,2)")]
    public decimal Price { get; set; }
    [Column(TypeName="decimal(18,2)")]
    public decimal OriginalPrice { get; set; }
}
{% endhighlight %}

  - `JsonIgnore` means this property is excluded from Json Serialization.

* BlazorEcommerce.Shared/ProductVariant.cs
{% highlight cs %}
public class ProductType
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
}
{% endhighlight %}

* BlazorEcommerce.Shared/ProductSearchResult.cs
{% highlight cs %}
public class ProductSearchResult
{
    public List<Product> Products { get; set; } = new List<Product>();
    public int Pages { get; set; }
    public int CurrentPage { get; set; }

}
{% endhighlight %}

### Seeds

* BlazorEcommerce.Server/Data/DataContext.cs
{% highlight cs %}
public class DataContext : DbContext
{
    public DataContext(DbContextOptions<DataContext> options) : base(options)
    {

    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<ProductVariant>()
            .HasKey(p => new { p.ProductId, p.ProductTypeId });

        modelBuilder.Entity<ProductType>().HasData(
            new ProductType { Id = 1, Name = "Default" },
            new ProductType { Id = 2, Name = "Paperback" },
            new ProductType { Id = 3, Name = "E-Book" },
            new ProductType { Id = 4, Name = "Audiobook" },
            new ProductType { Id = 5, Name = "Stream" },
            new ProductType { Id = 6, Name = "Blu-ray" },
            new ProductType { Id = 7, Name = "VHS" },
            new ProductType { Id = 8, Name = "PC" },
            new ProductType { Id = 9, Name = "PlayStation" },
            new ProductType { Id = 10, Name = "Xbox" });

        modelBuilder.Entity<Category>().HasData(
            new Category
            {
                Id = 1,
                Name = "Books",
                Url = "books"
            },
            new Category
            {
                Id = 2,
                Name = "Movies",
                Url = "movies"
            },
            new Category
            {
                Id = 3,
                Name = "Video Games",
                Url = "video-games"
            });

        modelBuilder.Entity<Product>().HasData(
            new Product
            {
                Id = 1,
                CategoryId = 1,
                Title = "The Hitchhiker's Guide to the Galaxy",
                Description = "The Hitchhiker's Guide to the Galaxy[note 1] (sometimes referred to as HG2G,[1] HHGTTG,[2] H2G2,[3] or tHGttG) is a comedy science fiction franchise created by Douglas Adams. Originally a 1978 radio comedy broadcast on BBC Radio 4, it was later adapted to other formats, including stage shows, novels, comic books, a 1981 TV series, a 1984 text-based computer game, and 2005 feature film.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/b/bd/H2G2_UK_front_cover.jpg",
                Featured = true
            },
            new Product
            {
                Id = 2,
                CategoryId = 1,
                Title = "Ready Player One",
                Description = "Ready Player One is a 2011 science fiction novel, and the debut novel of American author Ernest Cline. The story, set in a dystopia in 2045, follows protagonist Wade Watts on his search for an Easter egg in a worldwide virtual reality game, the discovery of which would lead him to inherit the game creator's fortune. Cline sold the rights to publish the novel in June 2010, in a bidding war to the Crown Publishing Group (a division of Random House).[1] The book was published on August 16, 2011.[2] An audiobook was released the same day; it was narrated by Wil Wheaton, who was mentioned briefly in one of the chapters.[3][4]Ch. 20 In 2012, the book received an Alex Award from the Young Adult Library Services Association division of the American Library Association[5] and won the 2011 Prometheus Award.[6]",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/a/a4/Ready_Player_One_cover.jpg"
            },
            new Product
            {
                Id = 3,
                CategoryId = 1,
                Title = "Nineteen Eighty-Four",
                Description = "Nineteen Eighty-Four (also stylised as 1984) is a dystopian social science fiction novel and cautionary tale written by English writer George Orwell. It was published on 8 June 1949 by Secker & Warburg as Orwell's ninth and final book completed in his lifetime. Thematically, it centres on the consequences of totalitarianism, mass surveillance and repressive regimentation of people and behaviours within society.[2][3] Orwell, a democratic socialist, modelled the totalitarian government in the novel after Stalinist Russia and Nazi Germany.[2][3][4] More broadly, the novel examines the role of truth and facts within politics and the ways in which they are manipulated.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/c/c3/1984first.jpg"
            },
            new Product
            {
                Id = 4,
                CategoryId = 2,
                Title = "The Matrix",
                Description = "The Matrix is a 1999 science fiction action film written and directed by the Wachowskis, and produced by Joel Silver. Starring Keanu Reeves, Laurence Fishburne, Carrie-Anne Moss, Hugo Weaving, and Joe Pantoliano, and as the first installment in the Matrix franchise, it depicts a dystopian future in which humanity is unknowingly trapped inside a simulated reality, the Matrix, which intelligent machines have created to distract humans while using their bodies as an energy source. When computer programmer Thomas Anderson, under the hacker alias \"Neo\", uncovers the truth, he \"is drawn into a rebellion against the machines\" along with other people who have been freed from the Matrix.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/c/c1/The_Matrix_Poster.jpg"
            },
            new Product
            {
                Id = 5,
                CategoryId = 2,
                Title = "Back to the Future",
                Description = "Back to the Future is a 1985 American science fiction film directed by Robert Zemeckis. Written by Zemeckis and Bob Gale, it stars Michael J. Fox, Christopher Lloyd, Lea Thompson, Crispin Glover, and Thomas F. Wilson. Set in 1985, the story follows Marty McFly (Fox), a teenager accidentally sent back to 1955 in a time-traveling DeLorean automobile built by his eccentric scientist friend Doctor Emmett \"Doc\" Brown (Lloyd). Trapped in the past, Marty inadvertently prevents his future parents' meeting—threatening his very existence—and is forced to reconcile the pair and somehow get back to the future.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/d/d2/Back_to_the_Future.jpg",
                Featured = true
            },
            new Product
            {
                Id = 6,
                CategoryId = 2,
                Title = "Toy Story",
                Description = "Toy Story is a 1995 American computer-animated comedy film produced by Pixar Animation Studios and released by Walt Disney Pictures. The first installment in the Toy Story franchise, it was the first entirely computer-animated feature film, as well as the first feature film from Pixar. The film was directed by John Lasseter (in his feature directorial debut), and written by Joss Whedon, Andrew Stanton, Joel Cohen, and Alec Sokolow from a story by Lasseter, Stanton, Pete Docter, and Joe Ranft. The film features music by Randy Newman, was produced by Bonnie Arnold and Ralph Guggenheim, and was executive-produced by Steve Jobs and Edwin Catmull. The film features the voices of Tom Hanks, Tim Allen, Don Rickles, Wallace Shawn, John Ratzenberger, Jim Varney, Annie Potts, R. Lee Ermey, John Morris, Laurie Metcalf, and Erik von Detten. Taking place in a world where anthropomorphic toys come to life when humans are not present, the plot focuses on the relationship between an old-fashioned pull-string cowboy doll named Woody and an astronaut action figure, Buzz Lightyear, as they evolve from rivals competing for the affections of their owner, Andy Davis, to friends who work together to be reunited with Andy after being separated from him.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/1/13/Toy_Story.jpg"

            },
            new Product
            {
                Id = 7,
                CategoryId = 3,
                Title = "Half-Life 2",
                Description = "Half-Life 2 is a 2004 first-person shooter game developed and published by Valve. Like the original Half-Life, it combines shooting, puzzles, and storytelling, and adds features such as vehicles and physics-based gameplay.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/2/25/Half-Life_2_cover.jpg"

            },
            new Product
            {
                Id = 8,
                CategoryId = 3,
                Title = "Diablo II",
                Description = "Diablo II is an action role-playing hack-and-slash computer video game developed by Blizzard North and published by Blizzard Entertainment in 2000 for Microsoft Windows, Classic Mac OS, and macOS.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/d/d5/Diablo_II_Coverart.png"
            },
            new Product
            {
                Id = 9,
                CategoryId = 3,
                Title = "Day of the Tentacle",
                Description = "Day of the Tentacle, also known as Maniac Mansion II: Day of the Tentacle, is a 1993 graphic adventure game developed and published by LucasArts. It is the sequel to the 1987 game Maniac Mansion.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/en/7/79/Day_of_the_Tentacle_artwork.jpg",
                Featured = true
            },
            new Product
            {
                Id = 10,
                CategoryId = 3,
                Title = "Xbox",
                Description = "The Xbox is a home video game console and the first installment in the Xbox series of video game consoles manufactured by Microsoft.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/4/43/Xbox-console.jpg"
            },
            new Product
            {
                Id = 11,
                CategoryId = 3,
                Title = "Super Nintendo Entertainment System",
                Description = "The Super Nintendo Entertainment System (SNES), also known as the Super NES or Super Nintendo, is a 16-bit home video game console developed by Nintendo that was released in 1990 in Japan and South Korea.",
                ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/e/ee/Nintendo-Super-Famicom-Set-FL.jpg"
            });

        modelBuilder.Entity<ProductVariant>().HasData(
            new ProductVariant
            {
                ProductId = 1,
                ProductTypeId = 2,
                Price = 9.99m,
                OriginalPrice = 19.99m
            },
            new ProductVariant
            {
                ProductId = 1,
                ProductTypeId = 3,
                Price = 7.99m
            },
            new ProductVariant
            {
                ProductId = 1,
                ProductTypeId = 4,
                Price = 19.99m,
                OriginalPrice = 29.99m
            },
            new ProductVariant
            {
                ProductId = 2,
                ProductTypeId = 2,
                Price = 7.99m,
                OriginalPrice = 14.99m
            },
            new ProductVariant
            {
                ProductId = 3,
                ProductTypeId = 2,
                Price = 6.99m
            },
            new ProductVariant
            {
                ProductId = 4,
                ProductTypeId = 5,
                Price = 3.99m
            },
            new ProductVariant
            {
                ProductId = 4,
                ProductTypeId = 6,
                Price = 9.99m
            },
            new ProductVariant
            {
                ProductId = 4,
                ProductTypeId = 7,
                Price = 19.99m
            },
            new ProductVariant
            {
                ProductId = 5,
                ProductTypeId = 5,
                Price = 3.99m,
            },
            new ProductVariant
            {
                ProductId = 6,
                ProductTypeId = 5,
                Price = 2.99m
            },
            new ProductVariant
            {
                ProductId = 7,
                ProductTypeId = 8,
                Price = 19.99m,
                OriginalPrice = 29.99m
            },
            new ProductVariant
            {
                ProductId = 7,
                ProductTypeId = 9,
                Price = 69.99m
            },
            new ProductVariant
            {
                ProductId = 7,
                ProductTypeId = 10,
                Price = 49.99m,
                OriginalPrice = 59.99m
            },
            new ProductVariant
            {
                ProductId = 8,
                ProductTypeId = 8,
                Price = 9.99m,
                OriginalPrice = 24.99m,
            },
            new ProductVariant
            {
                ProductId = 9,
                ProductTypeId = 8,
                Price = 14.99m
            },
            new ProductVariant
            {
                ProductId = 10,
                ProductTypeId = 1,
                Price = 159.99m,
                OriginalPrice = 299m
            },
            new ProductVariant
            {
                ProductId = 11,
                ProductTypeId = 1,
                Price = 79.99m,
                OriginalPrice = 399m
            });
    }

    public DbSet<Product> Products { get; set; }
    public DbSet<Category> Categories { get; set; }
    public DbSet<ProductType> ProductTypes { get; set; }
    public DbSet<ProductVariant> ProductVariants { get; set; }
}
{% endhighlight %}

### Service Responce with Generics

* BlazorEcommerce.Shared/ServiceResponse.cs
{% highlight cs %}
public class ServiceResponse<T>
{
    public T? Data { get; set; }
    public bool Success { get; set; } = true;
    public string Message { get; set; } = string.Empty;
}
{% endhighlight %}

  - On `T` every variable types can be posited. 


## Product Service in Server

### Interface

* BlazorEcommerce.Server/Services/ProductService/IProductService.cs
{% highlight cs %}
public interface IProductService
{
    Task<ServiceResponse<List<Product>>> GetProductsAsync();
    Task<ServiceResponse<Product>> GetProductAsync(int productId);
    Task<ServiceResponse<List<Product>>> GetProductsByCategory(string categoryUrl);
    Task<ServiceResponse<ProductSearchResult>> SearchProducts(string searchText, int page);
    Task<ServiceResponse<List<string>>> GetProductSearchSuggestions(string searchText);
    Task<ServiceResponse<List<Product>>> GetFeaturedProducts();
}
{% endhighlight %}

### Implement

* BlazorEcommerce.Server/Services/ProductService/ProductService.cs
{% highlight cs %}
public class ProductService : IProductService
{
    private readonly DataContext _context;

    public ProductService(DataContext context)
    {
        _context = context;
    }

    public async Task<ServiceResponse<List<Product>>> GetProductsAsync()
    {
        var response = new ServiceResponse<List<Product>>
        {
            Data = await _context.Products.Include(p => p.Variants).ToListAsync()
        };

        return response;
    }

    public async Task<ServiceResponse<Product>> GetProductAsync(int productId)
    {
        var response = new ServiceResponse<Product>();
        var product = await _context.Products
            .Include(p => p.Variants)
            .ThenInclude(v => v.ProductType)
            .FirstOrDefaultAsync(p => p.Id == productId);
        if(product == null)
        {
            response.Success = false;
            response.Message = "Sorry, but this product does not exist.";
        }
        else
        {
            response.Data = product;
        }

        return response;
    }

    public async Task<ServiceResponse<List<Product>>> GetProductsByCategory(string categoryUrl)
    {
        var response = new ServiceResponse<List<Product>>
        {
            Data = await _context.Products
                .Where(p => p.Category.Url.ToLower().Equals(categoryUrl.ToLower()))
                .Include(p => p.Variants)
                .ToListAsync()
        };

        return response;
    }

    public async Task<ServiceResponse<ProductSearchResult>> SearchProducts(string searchText, int page)
    {
        var pageResults = 2f;
        var pageCount = Math.Ceiling((await FindProductsBySearchText(searchText)).Count / pageResults);
        var products = await _context.Products
                            .Where(p => p.Title.ToLower().Equals(searchText.ToLower())
                            || p.Description.ToLower().Contains(searchText.ToLower()))
                            .Include(p => p.Variants)
                            .Skip((page - 1) * (int)pageResults)
                            .Take((int)pageResults)
                            .ToListAsync();

        var response = new ServiceResponse<ProductSearchResult>
        {
            Data = new ProductSearchResult
            {
                Products = products,
                CurrentPage = page,
                Pages = (int)pageCount
            }
        };

        return response;
    }

    private async Task<List<Product>> FindProductsBySearchText(string searchText)
    {
        return await _context.Products
                            .Where(p => p.Title.ToLower().Equals(searchText.ToLower())
                            || p.Description.ToLower().Contains(searchText.ToLower()))
                            .Include(p => p.Variants)
                            .ToListAsync();
    }

    public async Task<ServiceResponse<List<string>>> GetProductSearchSuggestions(string searchText)
    {
        var products = await FindProductsBySearchText(searchText);
        List<string> result = new List<string>();

        foreach(var product in products)
        {
            if(product.Title.Contains(searchText, StringComparison.OrdinalIgnoreCase))
                result.Add(product.Title);

            if(product.Description != null)
            {
                var punctuation = product.Description.Where(char.IsPunctuation)
                    .Distinct().ToArray();
                var words = product.Description.Split()
                    .Select(s => s.Trim(punctuation));

                foreach(var word in words)
                {
                    if(word.Contains(searchText, StringComparison.OrdinalIgnoreCase) && !result.Contains(word))
                        result.Add(word);
                }
            }
        }

        return new ServiceResponse<List<string>> { Data = result };
    }

    public async Task<ServiceResponse<List<Product>>> GetFeaturedProducts()
    {
        var response = new ServiceResponse<List<Product>>
        {
            Data = await _context.Products
                .Where(p => p.Featured)
                .Include(p => p.Variants)
                .ToListAsync()
        };

        return response;
    }
}
{% endhighlight %}

  - `Include` include overlapped data to return value. 
  - `ThenInclude` is for multi level of `Include`.
  - `Math.Ceiling` round up the value.
  - `Skip` skips over the first n elements in the sequence and returns a new sequence containing the remaining elements after the first n elements.
  - `Take` extracts the first n elements from the beginning of the target sequence and returns a new sequence containing only the elements taken.

### Controller
* BlazorEcommerce.Server/Controllers/ProductController.cs

{% highlight cs %}
[Route("api/[controller]")]
[ApiController]
public class ProductController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductController(IProductService productService)
    {
        _productService = productService;
    }

    [HttpGet]
    public async Task<ActionResult<ServiceResponse<List<Product>>>> GetProduct()
    {
        var result = await _productService.GetProductsAsync();
        return Ok(result);
    }

    [HttpGet("{productId}")]
    public async Task<ActionResult<ServiceResponse<Product>>> GetProduct(int productId)
    {
        var result = await _productService.GetProductAsync(productId);
        return Ok(result);
    }

    [HttpGet("category/{categoryUrl}")]
    public async Task<ActionResult<ServiceResponse<List<Product>>>> GetProductsByCategory(string categoryUrl)
    {
        var result = await _productService.GetProductsByCategory(categoryUrl);
        return Ok(result);
    }

    [HttpGet("search/{searchText}/{page}")]
    public async Task<ActionResult<ServiceResponse<ProductSearchResult>>> SearchProducts(string searchText, int page = 1)
    {
        var result = await _productService.SearchProducts(searchText, page);
        return Ok(result);
    }

    [HttpGet("searchsuggestions/{searchText}")]
    public async Task<ActionResult<ServiceResponse<List<string>>>> GetProductSearchSuggestions(string searchText)
    {
        var result = await _productService.GetProductSearchSuggestions(searchText);
        return Ok(result);
    }

    [HttpGet("featured")]
    public async Task<ActionResult<ServiceResponse<List<Product>>>> GetFeaturedProduct()
    {
        var result = await _productService.GetFeaturedProducts();
        return Ok(result);
    }
}
{% endhighlight %}

### Dependency Injection
* BlazorEcommerce.Server/Program.cs

{% highlight cs %}
...
builder.Services.AddScoped<ICartService, CartService>();
...
{% endhighlight %}

## Product Service in Client

### Interface

* BlazorEcommerce.Client/Services/ProductService/IProductService.cs
{% highlight cs %}
public interface IProductService
{
    event Action ProductsChanged;

    List<Product> Products { get; set; }
    string Message { get; set; }
    int CurrentPage { get; set; }
    int PageCount { get; set; }
    string LastSearchText { get; set; }
    Task GetProducts(string? categoryUrl = null);
    Task<ServiceResponse<Product>> GetProduct(int productId);
    Task SearchProducts(string searchText, int page);
    Task<List<string>> GetProductSearchSuggestions(string searchText);
}
{% endhighlight %}

### Interface

* BlazorEcommerce.Client/Services/ProductService/ProductService.cs
{% highlight cs %}
public class ProductService : IProductService
{
    private readonly HttpClient _http;

    public ProductService(HttpClient http)
    {
        _http = http;
    }

    public List<Product> Products { get; set; } = new List<Product>();
    public string Message { get; set; } = "Loading Products...";
    public int CurrentPage { get; set; } = 1;
    public int PageCount { get; set; } = 0;
    public string LastSearchText { get; set; } = string.Empty;

    public event Action ProductsChanged;
    public async Task<ServiceResponse<Product>> GetProduct(int productId)
    {
        var result = await _http.GetFromJsonAsync<ServiceResponse<Product>>($"api/product/{productId}");
        return result;
    }

    public async Task GetProducts(string? categoryUrl = null)
    {
        var result = categoryUrl == null ?
            await _http.GetFromJsonAsync<ServiceResponse<List<Product>>>("api/product/featured") :
            await _http.GetFromJsonAsync<ServiceResponse<List<Product>>>($"api/product/category/{categoryUrl}");
        if(result != null && result.Data != null)
            Products = result.Data;

        CurrentPage = 1;
        PageCount = 0;

        if (!Products.Any())
            Message = "No Product Found!";
        ProductsChanged.Invoke();
    }

    public async Task<List<string>> GetProductSearchSuggestions(string searchText)
    {
        var result = await _http.GetFromJsonAsync<ServiceResponse<List<string>>>($"api/product/searchsuggestions/{searchText}");
        return result.Data;
    }

    public async Task SearchProducts(string searchText, int page)
    {
        LastSearchText = searchText;
        var result = await _http.GetFromJsonAsync<ServiceResponse<ProductSearchResult>>($"api/product/search/{searchText}/{page}");
        if (result != null && result.Data != null)
        {
            Products = result.Data.Products;
            CurrentPage = result.Data.CurrentPage;
            PageCount = result.Data.Pages;
        }
        if (!Products.Any())
            Message = "No Products found.";
        ProductsChanged?.Invoke();
    }
}
{% endhighlight %}

### Dependency Injection 
* BlazoreEcommerce.Client/Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<IProductService, ProductService>();
...
{% endhighlight %}

# Category

## Category Service in Server

### interface

* BlazoreEcommerce.Server/Services/CategoryService/ICategoryService.cs
{% highlight cs %}
public interface ICategoryService
{
    Task<ServiceResponse<List<Category>>> GetCategories();
}
{% endhighlight %}

### Implement

* BlazoreEcommerce.Server/Services/CategoryService/CategoryService.cs
{% highlight cs %}
public class CategoryService : ICategoryService
{
    private readonly DataContext _context;

    public CategoryService(DataContext context)
    {
        _context = context;
    }

    public async Task<ServiceResponse<List<Category>>> GetCategories()
    {
        var categories = await _context.Categories.ToListAsync();
        return new ServiceResponse<List<Category>>
        {
            Data = categories
        };
    }
}
{% endhighlight %}

### Controller 

* BlazoreEcommerce.Server/Controllers/CategoryController.cs
{% highlight cs %}
[Route("api/[controller]")]
[ApiController]
public class CategoryController : ControllerBase
{
    private readonly ICategoryService _categoryService;

    public CategoryController(ICategoryService categoryService)
    {
        _categoryService = categoryService;
    }

    [HttpGet]
    public async Task<ActionResult<ServiceResponse<List<Category>>>> GetCategories()
    {
        var result = await _categoryService.GetCategories();
        return Ok(result);
    }
}
{% endhighlight %}

### Dependency Injection

* BlazorEcommerce.Server/Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<ICategoryService, CategoryService>();
...
{% endhighlight %}

## Category Service in Client

### Interface

* BlazorEcommerce.Client/Services/CategoryService/ICategoryService.cs
{% highlight cs %}
public interface ICategoryService
{
    List<Category> Categories { get; set; }
    Task GetCategories();
}
{% endhighlight %}

### Implement

* BlazorEcommerce.Client/Services/CategoryService/CategoryService.cs
{% highlight cs %}
public class CategoryService : ICategoryService
{
    private readonly HttpClient _http;

    public CategoryService(HttpClient http)
    {
        _http = http;
    }

    public List<Category> Categories { get; set; } = new List<Category>();

    public async Task GetCategories()
    {
        var response = await _http.GetFromJsonAsync<ServiceResponse<List<Category>>>("api/category");
        if(response != null && response.Data != null)
            Categories = response.Data;
    }
}
{% endhighlight %}

### Dependency Injection

* BlazorEcommercs.Client/Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<ICategoryService, CategoryService>();
...
{% endhighlight %}

# Cart

## Local Storage
  - To save cart data, you can use local storage in bkazor application.

### Package in Client
  - Download `Blazored.LocalStorage`.

<figure>
  <a href="/assets/img/posts/blazor_ecommerce/10.jpg"><img src="/assets/img/posts/blazor_ecommerce/10.jpg"></a>
  <figcaption>Package in Client</figcaption>
</figure>

### Set

* BlazorEcommercs.Client/Program.cs
{% highlight cs %}
...
builder.Services.AddBlazoredLocalStorage();
...
{% endhighlight %}

## Models

* BlazorEcommerce.Shared/CartItem.cs
{% highlight cs %}
public class CartItem
{
    public int ProductId { get; set; }
    public int ProductTypeId { get; set; }
    public int Quantity { get; set; } = 1;
}
{% endhighlight %}

* BlazorEcommerce.Shared/CartProductResponse.cs
{% highlight cs %}
public class CartProductResponse
{
    public int ProductId { get; set; }
    public string Title { get; set; } = string.Empty;
    public int ProductTypeId { get; set; }
    public string ProductType { get; set; } = string.Empty;
    public string ImageUrl { get; set; } = string.Empty;
    public decimal Price { get; set; }
    public int Quantity { get; set; }
}
{% endhighlight %}

## Cart Service in Server

### Interface

* BlazorEcommerce.Server/Services/ICartService.cs
{% highlight cs %}
public interface ICartService
{
    Task<ServiceResponse<List<CartProductResponse>>> GetCartProducts(List<CartItem> cartItems);
}
{% endhighlight %}

### Implement

* BlazorEcommerce.Server/Services/CartService.cs
{% highlight cs %}
public class CartService : ICartService
{
    private DataContext _context;

    public CartService(DataContext context)
    {
        _context = context;
    }

    public async Task<ServiceResponse<List<CartProductResponse>>> GetCartProducts(List<CartItem> cartItems)
    {
        var result = new ServiceResponse<List<CartProductResponse>>()
        {
            Data = new List<CartProductResponse>()
        };

        foreach(var item in cartItems)
        {
            var product = await _context.Products
                .Where(p => p.Id == item.ProductId)
                .FirstOrDefaultAsync();

            if (product == null)
                continue;

            var productVariant = await _context.ProductVariants
                .Where(v => v.ProductId == item.ProductId
                && v.ProductTypeId == item.ProductTypeId)
                .Include(v => v.ProductType)
                .FirstOrDefaultAsync();

            if (productVariant == null)
                continue;

            var cartProduct = new CartProductResponse
            {
                ProductId = product.Id,
                Title = product.Title,
                ImageUrl = product.ImageUrl,
                Price = productVariant.Price,
                ProductType = productVariant.ProductType.Name,
                ProductTypeId = productVariant.ProductTypeId,
                Quantity = item.Quantity
            };
            
            result.Data.Add(cartProduct);
        }

        return result;
    }
}
{% endhighlight %}

### Dependency Injection

* BlazorEcommerce.Server/Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<ICartService, CartService>();
...
{% endhighlight %}

## Cart Service in Client

### Interface

* BlazorEcommerce.Client/Services/ICartService.cs
{% highlight cs %}
public interface ICartService
{
    event Action OnChange;
    Task AddToCart(CartItem cartItem);
    Task<List<CartItem>> GetCartItems();
    Task<List<CartProductResponse>> GetCartProducts();
    Task RemoveProductFromCart(int productId, int productTypeId);
    Task UpdateQuantity(CartProductResponse product);
}
{% endhighlight %}

* BlazorEcommerce.Client/Services/CartService.cs
{% highlight cs %}
public class CartService : ICartService
{
    ILocalStorageService _localStorage;
    private readonly HttpClient _http;

    public CartService(ILocalStorageService localStorage, HttpClient http)
    {
        _localStorage = localStorage;
        _http = http;
    }

    public event Action OnChange;

    public async Task AddToCart(CartItem cartItem)
    {
        var cart = await _localStorage.GetItemAsync<List<CartItem>>("cart");
        if(cart == null)
            cart = new List<CartItem>();

        var sameItem = cart.Find(x => x.ProductId == cartItem.ProductId &&
            x.ProductTypeId == cartItem.ProductTypeId);
        if(sameItem == null)
            cart.Add(cartItem);
        else
            sameItem.Quantity += cartItem.Quantity;

        await _localStorage.SetItemAsync("cart", cart);
        OnChange.Invoke();
    }

    public async Task<List<CartItem>> GetCartItems()
    {
        var cart = await _localStorage.GetItemAsync<List<CartItem>>("cart");
        if (cart == null)
            cart = new List<CartItem>();
        return cart;
    }

    public async Task<List<CartProductResponse>> GetCartProducts()
    {
        var cartItems = await _localStorage.GetItemAsync<List<CartItem>>("cart");
        var response = await _http.PostAsJsonAsync("api/cart/products", cartItems);
        var cartProducts =
            await response.Content.ReadFromJsonAsync<ServiceResponse<List<CartProductResponse>>>();
        return cartProducts.Data;
    }

    public async Task RemoveProductFromCart(int productId, int productTypeId)
    {
        var cart = await _localStorage.GetItemAsync<List<CartItem>>("cart");
        if (cart == null)
            return;

        var cartItem = cart.Find(x => x.ProductId == productId
        && x.ProductTypeId == productTypeId);
        if (cartItem != null)
        {
            cart.Remove(cartItem);
            await _localStorage.SetItemAsync("cart", cart);
            OnChange.Invoke();
        }
    }

    public async Task UpdateQuantity(CartProductResponse product)
    {
        var cart = await _localStorage.GetItemAsync<List<CartItem>>("cart");
        if (cart == null)
            return;

        var cartItem = cart.Find(x => x.ProductId == product.ProductId
        && x.ProductTypeId == product.ProductTypeId);
        if (cartItem != null)
        {
            cartItem.Quantity = product.Quantity;
            await _localStorage.SetItemAsync("cart", cart);
        }
    }
}
{% endhighlight %}

  - `GetItemAsync` get data from local storage with key word.
  - `SetItemAsync` set data to local storage with key word.
  - `api/cart/products` is a post method in server. It will return data enveloped in Content.

### Dependency Injection

* BlazorEcommerce.Client/Program.cs
{% highlight cs %}
...
builder.Services.AddScoped<ICartService, CartService>();
...
{% endhighlight %}

# View

## Layout

### Layout

* BlazorEcommerce.Client/Shared/ShopLayout.razor
{% highlight razor %}
<div class="page">
    <main>
        <div class="top-row px-2">
            <HomeButton/>
            <Search/>
            <CartCounter/>
        </div>

        <div class="nav-menu">
            <ShopNavMenu />
        </div>

        <article class="content px-2">
            @Body
        </article>
    </main>
</div>
{% endhighlight %}

* BlazorEcommerce.Client/Shared/ShopLayout.razor.cs
{% highlight cs %}
public partial class ShopLayout : ComponentBase
{
    [Parameter]
    public RenderFragment? Body { get; set; }
}
{% endhighlight %}

* BlazorEcommerce.Client/Shared/ShopLayout.razor.css
{% highlight css %}
.page {
    position: relative;
    display: flex;
    flex-direction: column;
}

main {
    flex: 1;
}

.nav-menu {
    background-image: linear-gradient(180deg, rgb(44, 62, 80) 0%, rgb(52, 73, 94) 70%);
}

.top-row {
    background-color: #f7f7f7;
    border-bottom: 1px solid #d6d5d5;
    justify-content: flex-end;
    height: 3.5rem;
    display: flex;
    align-items: center;
}

    .top-row ::deep a, .top-row ::deep .btn-link {
        white-space: nowrap;
        margin-left: .5rem;
        text-decoration: none;
    }

    .top-row ::deep a:hover, .top-row ::deep .btn-link:hover {
        text-decoration: underline;
    }

    .top-row ::deep a:first-child {
        overflow: hidden;
        text-overflow: ellipsis;
    }

@media (max-width: 640.98px) {
    .top-row.auth {
        justify-content: space-between;
    }

    .top-row ::deep a, .top-row ::deep .btn-link {
        margin-left: 0;
    }
}

@media (min-width: 641px) {
    .page {
        flex-direction: row;
    }

    .top-row {
        position: sticky;
        top: 0;
        z-index: 1;
    }

    .top-row.auth ::deep a:first-child {
        flex: 1;
        text-align: right;
        width: 0;
    }

    .top-row, article {
        padding-left: 2rem !important;
        padding-right: 1.5rem !important;
    }
}
{% endhighlight %}

### Set Layout in App

* BlazorEcommerce.Client/App.razor
{% highlight razor %}
<Router AppAssembly="@typeof(App).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(ShopLayout)" />
        <FocusOnNavigate RouteData="@routeData" Selector="h1" />
    </Found>
    <NotFound>
        <PageTitle>Not found</PageTitle>
        <LayoutView Layout="@typeof(ShopLayout)">
            <p role="alert">Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>
{% endhighlight %}

## Home 

### Home Button

* BlazorEcommerce.Client/Shared/HomeButton.razor
{% highlight razor %}
<button @onclick="GoToHome"
    class="btn btn-outline-primary home-button">
    My Shop
</button>
{% endhighlight %}

* BlazorEcommerce.Client/Shared/HomeButton.razor.cs
{% highlight cs %}
public partial class HomeButton
{
    [Inject]
    public NavigationManager NavigationManager { get; set; }

    private void GoToHome()
    {
        NavigationManager.NavigateTo("");
    }
}
{% endhighlight %}

* BlazorEcommerce.Client/Shared/HomeButton.razor.css
{% highlight css %}
.home-button {
    white-space: nowrap;
    margin: 10px;
    transform: rotate(-5deg);
}
{% endhighlight %}

### Search

* BlazorEcommerce.Client/Shared/Search.razor
{% highlight razor %}
<div class="input-group">
    <input @bind-value="searchText" 
           @bind-value:event="oninput"
           type="search"
           list="products"
           @onkeyup="HandleSearch"
           class="form-control"
           placeholder="Search..."
           @ref="searchInput" />
    <datalist id="products">
        @foreach(var suggestion in suggestions)
        {
            <option>@suggestion</option>
        }
    </datalist>
    <div class="input-group-append">
        <button class="btn btn-primary" @onclick="SearchProducts">
            <span class="oi oi-magnifying-glass"></span>
        </button>
    </div>
</div>
{% endhighlight %}

* BlazorEcommerce.Client/Shared/Search.razor.cs
{% highlight cs %}
public partial class Search
{
    [Inject]
    public NavigationManager NavigationManager { get; set; }
    [Inject]
    public IProductService ProductService { get; set; }

    private string searchText = string.Empty;
    private List<string> suggestions = new List<string>();
    protected ElementReference searchInput;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if(firstRender)
            await searchInput.FocusAsync();
    }

    public void SearchProducts()
    {
        NavigationManager.NavigateTo($"search/{searchText}/1");
    }

    public async Task HandleSearch(KeyboardEventArgs args)
    {
        if (args.Key == null || args.Key.Equals("Enter"))
            SearchProducts();
        else if (searchText.Any())
            suggestions = await ProductService.GetProductSearchSuggestions(searchText);
    }
}
{% endhighlight %}

### Cart Counter

* BlazorEcommerce.Client/Shared/CartCounter.razor
{% highlight razor %}
<a href="cart" class="btn btn-info">
    <i class="oi oi-cart"></i>
    <span class="badge">@GetCartItemCount()</span>
</a>
{% endhighlight %}

* BlazorEcommerce.Client/Shared/CartCounter.razor.cs
{% highlight cs %}
public partial class CartCounter : IDisposable
{
    [Inject]
    ICartService CartService { get; set; }
    
    [Inject]
    ISyncLocalStorageService LocalStorage { get; set; }

    private int GetCartItemCount()
    {
        var cart = LocalStorage.GetItem<List<CartItem>>("cart");
        return cart != null ? cart.Count : 0;
    }

    protected override void OnInitialized()
    {
        CartService.OnChange += StateHasChanged;
    }

    public void Dispose()
    {
        CartService.OnChange -= StateHasChanged;
    }
}
{% endhighlight %}

  - `IDisposable` implements to disubscreib the event.

### Shop Nav Menu

* BlazorEcommerce.Client/Shared/ShopNavMenu.razor
{% highlight razor %}
<div class="top-row ps-3 navbar navbar-dark navbar-toggler-wrapper">
    <div class="container-fluid">
        <a class="navbar-brand" href="">BlazorEcommerce</a>
        <button title="Navigation menu" class="navbar-toggler" @onclick="ToggleNavMenu">
            <span class="navbar-toggler-icon"></span>
        </button>
    </div>
</div>

<div class="@NavMenuCssClass" @onclick="ToggleNavMenu">
    <nav class="flex-nav">
        <div class="nav-item px-2">
            <NavLink class="nav-link" href="" Match="NavLinkMatch.All">
                Home
            </NavLink>
        </div>
        @foreach(var category in CategoryService.Categories)
        {
            <div class="nav-item px-2">
                <NavLink class="nav-link" href="@category.Url">
                    @category.Name
                </NavLink>
            </div>
        }
    </nav>
</div>
{% endhighlight %}

* BlazorEcommerce.Client/Shared/ShopNavMenu.razor.cs
{% highlight cs %}
public partial class ShopNavMenu
{
    [Inject]
    public ICategoryService CategoryService { get; set; }

    protected override async Task OnInitializedAsync()
    {
        await CategoryService.GetCategories();
    }

    private bool collapseNavMenu = true;

    private string? NavMenuCssClass => collapseNavMenu ? "collapse" : null;

    private void ToggleNavMenu()
    {
        collapseNavMenu = !collapseNavMenu;
    }
}
{% endhighlight %}

* BlazorEcommerce.Client/Shared/ShopNavMenu.razor.css
{% highlight css %}
.navbar-toggler {
    color: rgba(255, 255, 255, 0.1);
}

.top-row {
    height: 3.5rem;
    background-color: rgba(0,0,0,0.4);
}

.navbar-brand {
    font-size: 1.1rem;
}

.oi {
    width: 2rem;
    font-size: 1.1rem;
    vertical-align: text-top;
    top: -2px;
}

.nav-item {
    font-size: 0.9rem;
    padding-bottom: 0.5rem;
    padding-top: 0.5rem;
}

    .nav-item ::deep a {
        color: #d7d7d7;
        border-radius: 4px;
        height: 3rem;
        display: flex;
        align-items: center;
        line-height: 3rem;
    }

.nav-item ::deep a.active {
    background-color: rgba(255,255,255,0.25);
    color: white;
}

.nav-item ::deep a:hover {
    background-color: rgba(255,255,255,0.1);
    color: white;
}

.flex-nav{
    flex-direction: column;
}

@media (min-width: 641px) {
    .navbar-toggler, .navbar-toggler-wrapper {
        display: none;
    }

    .flex-nav {
        flex-direction: row;
        display: flex;
    }

    .collapse {
        /* Never collapse the sidebar for wide screens */
        display: block;
    }
}
{% endhighlight %}

### Body

* BlazorEcommerce.Client/Pages/Index.razor
{% highlight razor %}
@page "/"
@page "/{categoryUrl}"
@page "/search/{searchText}/{page:int}"

@if(SearchText == null && CategoryUrl == null)
{
    <FeaturedProducts/>
}
else
{
    <ProductList/>
}
{% endhighlight %}

* BlazorEcommerce.Client/Pages/Index.razor.cs
{% highlight cs %}
public partial class Index
{
    [Inject]
    public IProductService ProductService { get; set; }

    [Parameter]
    public string? CategoryUrl { get; set; } = null;
    [Parameter]
    public string? SearchText { get; set; } = null;
    [Parameter]
    public int Page { get; set; } = 1;

    protected override async Task OnParametersSetAsync()
    {
        if(SearchText != null)
            await ProductService.SearchProducts(SearchText, Page);
        else
            await ProductService.GetProducts(CategoryUrl);
    }
}
{% endhighlight %}

## Products

### Featured Products

* BlazorEcommerce.Client/Shared/FeaturedProducts.razor
{% highlight razor %}

<center><h2>Top Products of Today</h2></center>
@if (!ProductService.Products.Any())
{
    <span>@ProductService.Message</span>
}
else
{
    <div class="container">
        @foreach(var product in ProductService.Products)
        {
            @if (product.Featured)
            {
                <div class="featured-product">
                    <div>
                        <a href="product/@product.Id">
                            <img src="@product.ImageUrl">
                        </a>
                    </div>
                    <h4><a href="product/@product.Id">@product.Title</a></h4>
                    @if(product.Variants.Any())
                    {
                        <h5 class="price">
                            $@product.Variants[0].Price
                        </h5>
                    }
                </div>
            }
        }
    </div>
}

{% endhighlight %}

* BlazorEcommerce.Client/Shared/FeaturedProducts.razor.cs
{% highlight cs %}
public partial class FeaturedProducts : IDisposable
{
    [Inject]
    public IProductService ProductService { get; set; }

    protected override void OnInitialized()
    {
        ProductService.ProductsChanged += StateHasChanged;
    }

    public void Dispose()
    {
        ProductService.ProductsChanged -= StateHasChanged;
    }
}
{% endhighlight %}

* BlazorEcommerce.Client/Shared/FeaturedProducts.razor.css
{% highlight css %}
.container {
    display: flex;
    flex-direction: row;
    overflow-x: auto;
    justify-content: center;
}

img {
    max-width: 200px;
    max-height: 200px;
    border-radius: 6px;
    transition: transform .2s;
    margin-bottom: 10px;
}

    img:hover{
        transform: scale(1.1) rotate(5deg);
    }

.featured-product {
    margin: 10px;
    text-align: center;
    padding: 10px;
    border: 1px solid lightgray;
    border-radius: 10px;
    max-width: 200px;
}

@media (max-width: 1023.98px) {
    .container {
        justify-content: flex-start;
    }
}
{% endhighlight %}

### Product List

* BlazorEcommerce.Client/Shared/ProductList.razor
{% highlight razor %}
@if (!ProductService.Products.Any())
{
    <span>@ProductService.Message</span>
}
else
{
    <ul class="list-unstyled">
        @foreach(var product in ProductService.Products)
        {
            <li class="media my-3">
                <div class="media-img-wrapper mr-2">
                    <a href="/product/@product.Id">
                        <img class="media-img" src="@product.ImageUrl" alt="@product.Title"/>
                    </a>
                </div>
                <div class="media-body">
                    <a href="/product/@product.Id">
                        <h4 class="mb-0">@product.Title</h4>
                    </a>
                    <p>@product.Description</p>
                    <h5 class="price">
                        @GetPriceText(product)
                    </h5>
                </div>
            </li>
        }
    </ul>
    {
        for(var i = 1; i <= ProductService.PageCount; i++)
        {
            <a class="btn @(i == ProductService.CurrentPage ? "btn-info" : "btn-outline-info") page-selection" href="/search/@ProductService.LastSearchText/@i">@i</a>
        }
    }
}
{% endhighlight %}

* BlazorEcommerce.Client/Shared/ProductList.razor.cs
{% highlight cs %}
public partial class ProductList : IDisposable
{
    [Inject]
    public IProductService ProductService { get; set; }

    protected override void OnInitialized()
    {
        ProductService.ProductsChanged += StateHasChanged;
    }

    public void Dispose()
    {
        ProductService.ProductsChanged -= StateHasChanged;
    }

    private string GetPriceText(Product product) 
    {
        var variants = product.Variants;
        if (!variants.Any())
            return string.Empty;
        else if (variants.Count == 1)
            return $"${variants[0].Price}";
        decimal minPrice = variants.Min(v => v.Price);
        return $"Starting at ${minPrice}";
    }
}
{% endhighlight %}

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

.media-img-wrapper {
    width: 200px;
    text-align: center;
}

.page-selection {
    margin-right: 15px;
    margin-bottom: 30px;
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

### Product Detail

* BlazorEcommerce.Client/Pages/ProductDetails.razor
{% highlight razor %}
@page "/product/{id:int}"

@if(product == null)
{
    <span>@message</span>
}
else
{
    <div class="media">
        <div class="media-img-wrapper mr-2">
            <img class="media-img" src="@product.ImageUrl" alt="@product.Title"/>
        </div>
        <div class="media-body">
            <h2 class="mb-0">@product.Title</h2>
            <p>@product.Description</p>
            @if (product.Variants.Any())
            {
                <div class="mb-3">
                    <select class="form-select" @bind="currentTypeId">
                        @foreach(var variant in product.Variants)
                        {
                            <option value="@variant.ProductTypeId">@variant.ProductType.Name</option>
                        }
                    </select>
                </div>
            }
            @if(GetSelectedVariant() != null)
            {
                @if(GetSelectedVariant().OriginalPrice > GetSelectedVariant().Price)
                {
                    <h6 class="text-muted original-price">
                        $@GetSelectedVariant().OriginalPrice
                    </h6>
                }
                <h4 class="price">
                    $@GetSelectedVariant().Price
                </h4>
            }
            <button class="btn btn-primary" @onclick="AddToCart">
                <i class="oi oi-cart"></i>&nbsp;&nbsp;&nbsp;Add to Cart
            </button>
        </div>
    </div>
}
{% endhighlight %}

* BlazorEcommerce.Client/Pages/ProductDetails.razor.cs
{% highlight cs %}
public partial class ProductDetails
{
    [Inject]
    public ICartService CartService { get; set; }

    [Inject]
    public IProductService ProductService { get; set; }

    private Product? product = null;
    private string message;
    private int currentTypeId = 1;

    [Parameter]
    public int Id { get; set; }

    protected override async Task OnParametersSetAsync()
    {
        message = "Loading roduct...";
        var result = await ProductService.GetProduct(Id);
        if (!result.Success)
            message = result.Message;
        else
        {
            product = result.Data;
            if (product.Variants.Any())
                currentTypeId = product.Variants[0].ProductTypeId;
        }
    }

    private ProductVariant GetSelectedVariant()
    {
        var variant = product.Variants.FirstOrDefault(v => v.ProductTypeId == currentTypeId);
        return variant;
    }

    private async Task AddToCart()
    {
        var productVariant = GetSelectedVariant();
        var cartItem = new CartItem
        {
            ProductId = productVariant.ProductId,
            ProductTypeId = productVariant.ProductTypeId
        };
        await CartService.AddToCart(cartItem);
    }
}
{% endhighlight %}

* BlazorEcommerce.Client/Pages/ProductDetails.razor.css
{% highlight css %}
.media-img {
    max-height: 500px;
    max-width: 500px;
    border-radius: 6px;
    margin: 0 10px 10px 10px;
}

.media-img-wrapper {
    max-width: 500px;
    max-height: 500px;
    text-align: center;
}

.original-price {
    text-decoration: line-through;
}

@media (max-width: 1023.98px) {
    .media {
        flex-direction: column;
    }

    .media-img {
        max-width: 300px;
    }

    .media-img-wrapper {
        width: 100%;
        height: auto;
    }
}
{% endhighlight %}

## Cart

* BlazorEcommerce.Client/Pages/Cart.razor
{% highlight razor %}
@page "/cart"

<h3>Shopping Cart</h3>

@if (!cartProducts.Any())
{
    <span>@message</span>
}
else
{
    <div>
        @foreach(var product in cartProducts)
        {
            <div class="container">
                <div class="image-wrapper">
                    <img src="@product.ImageUrl" class="image"/>
                </div>
                <div class="name">
                    <h5><a href="/product/@product.ProductId">@product.Title</a></h5>
                    <span>@product.ProductType</span><br/>
                    <input type="number" value="@product.Quantity" @onchange="@((ChangeEventArgs e) => UpdateQuantity(e, product))" class="form-control input-quantity" min="1"/>
                    <button class="btn-delete" @onclick="@(() => RemoveProductFromCart(product.ProductId, product.ProductTypeId))">Delete</button>
                </div>
                <div class="cart-product-price">$@(product.Price * product.Quantity)</div>
            </div>
        }
        <div class="cart-product-price">
            Total (@cartProducts.Count): $@cartProducts.Sum(product => @product.Price * product.Quantity)
        </div>
    </div>
}
{% endhighlight %}

* BlazorEcommerce.Client/Pages/Cart.razor.cs
{% highlight cs %}
public partial class Cart
{
    [Inject]
    public ICartService CartService { get; set; }

    List<CartProductResponse> cartProducts = new List<CartProductResponse>();
    string message = "Loading cart...";

    protected override async Task OnInitializedAsync()
    {
        await LoadCart();
    }

    private async Task RemoveProductFromCart(int productId, int productTypeId)
    {
        await CartService.RemoveProductFromCart(productId, productTypeId);
        await LoadCart();
    }
    private async Task LoadCart()
    {
        if (!(await CartService.GetCartItems()).Any())
        {
            message = "Your cart is empty.";
            cartProducts = new List<CartProductResponse>();
        }
        else
        {
            cartProducts = await CartService.GetCartProducts();
        }
    }

    private async Task UpdateQuantity(ChangeEventArgs e, CartProductResponse product)
    {
        product.Quantity = int.Parse(e.Value.ToString());
        if (product.Quantity < 1)
            product.Quantity = 1;
        await CartService.UpdateQuantity(product);
    }
}
{% endhighlight %}

* BlazorEcommerce.Client/Pages/Cart.razor.css
{% highlight css %}
.container {
    display: flex;
    padding: 6px;
}

.image-wrapper {
    width: 150px;
    text-align: center;
}

.image {
    max-height: 150px;
    max-width: 150px;
    padding: 6px;
}

.name{
    flex-grow: 1;
    padding: 6px;
}

.cart-product-price {
    font-weight: 600;
    text-align: right;
}

.btn-delete {
    background: none;
    border: none;
    padding: 0px;
    color: red;
    font-size: 12px;
}

    .btn-delete:hover {
        text-decoration: underline;
    }

.input-quantity{
    width: 70px;
}
{% endhighlight %}

## Show

<figure class="third">
  <a href="/assets/img/posts/blazor_ecommerce/11.jpg"><img src="/assets/img/posts/blazor_ecommerce/11.jpg"></a>
  <a href="/assets/img/posts/blazor_ecommerce/12.jpg"><img src="/assets/img/posts/blazor_ecommerce/12.jpg"></a>
  <a href="/assets/img/posts/blazor_ecommerce/13.jpg"><img src="/assets/img/posts/blazor_ecommerce/13.jpg"></a>
  <figcaption>Package in Client</figcaption>
</figure>

<figure class="half">
  <a href="/assets/img/posts/blazor_ecommerce/14.jpg"><img src="/assets/img/posts/blazor_ecommerce/14.jpg"></a>
  <a href="/assets/img/posts/blazor_ecommerce/15.jpg"><img src="/assets/img/posts/blazor_ecommerce/15.jpg"></a>
  <figcaption>Package in Client</figcaption>
</figure>

# Code

[Download](https://github.com/leehuhlee/BlazorEcommerce){: .btn}