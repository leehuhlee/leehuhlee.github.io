---
layout: post
title: "[.Net 6] Angular SuperHero"
date: 2022-09-27
excerpt: "Angular"
tags: [C#, .Net6, Angular, WebAPI]
comments: false
---

# Create the Angular Application

## Create Project
  - Create Project Folder named SuperHeroNG

<figure>
  <a href="/assets/img/posts/angular_superhero/0.jpg"><img src="/assets/img/posts/angular_superhero/0.jpg"></a>
  <figcaption>Create the Angular Application</figcaption>
</figure>

  - Download <a href="https://nodejs.org/en/download/">Node.js</a>

<figure>
  <a href="/assets/img/posts/angular_superhero/1.jpg"><img src="/assets/img/posts/angular_superhero/1.jpg"></a>
  <figcaption>Create the Angular Application</figcaption>
</figure>

  - Open project forder in Visual Studio Code 
  - Set powershell policy with  `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`
  - Download Angular CLI with `npm install -g @angular/cli`
  - Create new angular projekt skeleton with `ng new SuperHero.UI` in SuperHeroNG folder

<figure class="half">
  <a href="/assets/img/posts/angular_superhero/2.jpg"><img src="/assets/img/posts/angular_superhero/2.jpg"></a>
  <a href="/assets/img/posts/angular_superhero/3.jpg"><img src="/assets/img/posts/angular_superhero/3.jpg"></a>
  <a href="/assets/img/posts/angular_superhero/4.jpg"><img src="/assets/img/posts/angular_superhero/4.jpg"></a>
  <figcaption>Create the Angular Application</figcaption>
</figure>

  - Add `components`, `models` and `services` folders in src/app
  - Run angular with `ng serve -o` in SuperHero.UI folder

<figure class="half">
  <a href="/assets/img/posts/angular_superhero/5.jpg"><img src="/assets/img/posts/angular_superhero/5.jpg"></a>
  <a href="/assets/img/posts/angular_superhero/6.jpg"><img src="/assets/img/posts/angular_superhero/6.jpg"></a>
  <figcaption>Create the Angular Application</figcaption>
</figure>

# Add the Super Hero Model to the Client

## Download Extensions

  - Download `Angular Language Service` in Visual Studio Code 

<figure>
  <a href="/assets/img/posts/angular_superhero/7.jpg"><img src="/assets/img/posts/angular_superhero/7.jpg"></a>
  <figcaption>Add the Super Hero Model to the Client</figcaption>
</figure>

## Super Hero Model

* SuperHero.UI/src/app/models/super-hero.ts
{% highlight ts %}
export class SuperHero {
    id?: number;
    name = "";
    firstName = "";
    lastName = "";
    place = "";
}
{% endhighlight %}

# Generate an Angular Service

## Service

  - Move to app\services
  - Create super-hero service with `ng g s super-hero --skip-tests`

<figure>
  <a href="/assets/img/posts/angular_superhero/8.jpg"><img src="/assets/img/posts/angular_superhero/8.jpg"></a>
  <figcaption>Generate an Angular Service</figcaption>
</figure>

* SuperHero.UI/src/app/services/super-hero.service.ts
{% highlight ts %}
...
export class SuperHeroService {

  constructor() { }

  public getSuperHeroes() : SuperHero[] {
    let hero = new SuperHero();
    hero.id = 1;
    hero.name = "Spider Man";
    hero.firstName = "Peter";
    hero.lastName = "Parker";
    hero.place = "New York City";

    return [hero];
  }
...
{% endhighlight %}

## Component

 - Clear app/app.component.html

* SuperHero.UI/src/app/app.component.ts
{% highlight ts %}
...
export class AppComponent {
  title = 'SuperHero.UI';
  heroes: SuperHero[] = [];

  constructor(private superHeroService: SuperHeroService) {}

  ngOnInit() : void {
    this.heroes = this.superHeroService.getSuperHeroes();
    console.log(this.heroes);
  }
...
{% endhighlight %}

## Run

  - Run with `ng serve -o`

<figure class="half">
  <a href="/assets/img/posts/angular_superhero/9.jpg"><img src="/assets/img/posts/angular_superhero/9.jpg"></a>
  <a href="/assets/img/posts/angular_superhero/10.jpg"><img src="/assets/img/posts/angular_superhero/10.jpg"></a>
  <figcaption>Generate an Angular Service</figcaption>
</figure>

# Implement a Table to Show the Hero List

* SuperHero.UI/src/app/app.component.html
{% highlight html %}
<table>
    <thead>
        <th>Name</th>
        <th>First Name</th>
        <th>Last Name</th>
        <th>Place</th>
    </thead>
    <tbody>
        <tr *ngFor="let hero of heroes">
            <td>{{ hero.name }}</td>
            <td>{{ hero.firstName }}</td>
            <td>{{ hero.lastName }}</td>
            <td>{{ hero.place }}</td>
        </tr>
    </tbody>
</table>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/angular_superhero/11.jpg"><img src="/assets/img/posts/angular_superhero/11.jpg"></a>
  <figcaption>Implement a Table to Show the Hero List</figcaption>
</figure>

# Create the .Net 6 Web API Project

  - Create a ASP.NET CORE WEB API project named SuperHeroAPI on same folder with UI in Visual Studio

<figure>
  <a href="/assets/img/posts/angular_superhero/12.jpg"><img src="/assets/img/posts/angular_superhero/12.jpg"></a>
  <figcaption>Implement a Table to Show the Hero List</figcaption>
</figure>

# Add the Super Hero Model to the API

* SuperHeroAPI/SuperHero.cs
{% highlight cs %}
public class SuperHero
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;    
    public string FirstName { get; set; } = string.Empty;    
    public string LastName { get; set; } = string.Empty;    
    public string Place { get; set; } = string.Empty;    
}
{% endhighlight %}

# Add the Super Hero Controller

* SuperHeroAPI/Controller/SuperHeroController.cs
{% highlight cs %}
[Route("api/[controller]")]
[ApiController]
public class SuperHeroController : ControllerBase
{
    [HttpGet]
    public async Task<ActionResult<List<SuperHero>>> GetSuperHeroes()
    {
        return new List<SuperHero>
        {
            new SuperHero
            {
                Name = "Spider Man",
                FirstName = "Peter",
                LastName = "Parker",
                Place = "New York City"
            }
        };
    }
}
{% endhighlight %}

## Call the Web API from the Angular 14 Client

  - Copy your Web API url and paste on your angular.

* SuperHero.UI/src/environment/environment.ts
{% highlight ts %}
export const environment = {
  production: false,
  apiUrl: "https://localhost:7169/api"
};
{% endhighlight %}

* SuperHero.UI/src/app/services/super-hero.service.ts
{% highlight ts %}
...
export class SuperHeroService {
  private url = "SuperHero";

  constructor(private http: HttpClient) { }

  public getSuperHeroes() : Observable<SuperHero[]> {
    return this.http.get<SuperHero[]>(`${environment.apiUrl}/${this.url}`);
  }
}
...
{% endhighlight %}

* SuperHero.UI/src/app/app.module.ts
{% highlight ts %}
...
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule
  ],
  ...
{% endhighlight %}

* SuperHero.UI/src/app/components/app.component.ts
{% highlight ts %}
...
ngOnInit() : void {
    this.superHeroService
      .getSuperHeroes()
      .subscribe((result: SuperHero[]) => (this.heroes = result));
  }
...
{% endhighlight %}

# Enable CORS(Cross-Origin Resource Sharing)

* SuperHero.API/Program.cs
{% highlight cs %}
builder.Services.AddCors(options => options.AddPolicy(name: "SuperHeroOrigins", // To Access the Origins
    policy =>
    {
        policy.WithOrigins("http://localhost:4200").AllowAnyMethod().AllowAnyHeader();
    }));
    ...
app.UseCors("SuperHeroOrigins");
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/angular_superhero/13.jpg"><img src="/assets/img/posts/angular_superhero/13.jpg"></a>
  <figcaption>Enable CORS(Cross-Origin Resource Sharing)</figcaption>
</figure>

# Add Entity Framework Core 6

  - Create Data folder in SuperHeroAPI

* SuperHero.API/Data/DataContext.cs
{% highlight cs %}
public class DataContext : DbContext
{
    public DataContext(DbContextOptions<DataContext> options) : base(options)
    {

    }

    public DbSet<SuperHero> SuperHeroes => Set<SuperHero>();
}
{% endhighlight %}

  - Download `Microsoft.EntityFrameworkCore.Design` and `Microsoft.EntityFrameworkCore.SqlServer` in Nuget Package manager

<figure>
  <a href="/assets/img/posts/angular_superhero/14.jpg"><img src="/assets/img/posts/angular_superhero/14.jpg"></a>
  <figcaption>Add Entity Framework Core 6</figcaption>
</figure>

# Add the Connection String for the SQL Server Database

* SuperHero.API/appsettings.json
{% highlight json %}
...
"ConnectionStrings": {
    "DefaultConnection": "server=localhost\\sqlexpress;database=superherodb;trusted_connection=true"
  },
  ...
{% endhighlight %}

# Install the EF Core Tools

  - Download Dotnet EntityCore Tools with `dotnet tool install --global dotnet-ef` and check with `dotnet ef`

<figure class="half">
  <a href="/assets/img/posts/angular_superhero/15.jpg"><img src="/assets/img/posts/angular_superhero/15.jpg"></a>
  <a href="/assets/img/posts/angular_superhero/16.jpg"><img src="/assets/img/posts/angular_superhero/16.jpg"></a>
  <figcaption>Install the EF Core Tools</figcaption>
</figure>

# Register the DataContext in the Program.cs

* SuperHero.API/Program.cs
{% highlight cs %}
builder.Services.AddDbContext<DataContext>(options =>
{
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});
{% endhighlight %}

# Run the Initial Migration using Code-First Migration

  - Migration data table with `dotnet ef migrations add Initial`

<figure>
  <a href="/assets/img/posts/angular_superhero/17.jpg"><img src="/assets/img/posts/angular_superhero/17.jpg"></a>
  <figcaption>Run the Initial Migration using Code-First Migration</figcaption>
</figure>

  - Update database with `dotnet ef database update`

<figure>
  <a href="/assets/img/posts/angular_superhero/18.jpg"><img src="/assets/img/posts/angular_superhero/18.jpg"></a>
  <figcaption>Run the Initial Migration using Code-First Migration</figcaption>
</figure>

# Get Super Heroes from the Database

* SuperHero.API/Controllers/SuperHeroController.cs
{% highlight cs %}
[Route("api/[controller]")]
[ApiController]
public class SuperHeroController : ControllerBase
{
    private readonly DataContext _context;

    public SuperHeroController(DataContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<ActionResult<List<SuperHero>>> GetSuperHeroes()
    {
        return Ok(await _context.SuperHeroes.ToListAsync());
    }
}
{% endhighlight %}

  - Add a row in database

<figure>
  <a href="/assets/img/posts/angular_superhero/18.jpg"><img src="/assets/img/posts/angular_superhero/18.jpg"></a>
  <figcaption>Run the Initial Migration using Code-First Migration</figcaption>
</figure>

# Implement Create, Update and Delete in the SuperHeroController

* SuperHero.API/Controllers/SuperHeroController.cs
{% highlight cs %}
...
[HttpPost]
public async Task<ActionResult<List<SuperHero>>> CreateSuperHero(SuperHero hero) 
{
    _context.SuperHeroes.Add(hero);
    await _context.SaveChangesAsync();

    return Ok(await _context.SuperHeroes.ToListAsync());
}

[HttpPut]
public async Task<ActionResult<List<SuperHero>>> UpdateSuperHero(SuperHero hero)
{
    var dbHero = await _context.SuperHeroes.FindAsync(hero.Id);
    if (dbHero == null)
        return BadRequest("Hero not found.");

    dbHero.Name = hero.Name;
    dbHero.FirstName = hero.FirstName;
    dbHero.LastName = hero.LastName;
    dbHero.Place = hero.Place;

    await _context.SaveChangesAsync();

    return Ok(await _context.SuperHeroes.ToListAsync());
}

[HttpDelete("{id}")]
public async Task<ActionResult<List<SuperHero>>> DeleteSuperHero(int id)
{
    var dbHero = await _context.SuperHeroes.FindAsync(id);
    if (dbHero == null)
        return BadRequest("Hero not found.");

    _context.SuperHeroes.Remove(dbHero);
    await _context.SaveChangesAsync();

    return Ok(await _context.SuperHeroes.ToListAsync()); 
}
...
{% endhighlight %}

# Build a Edit-Hero Component with Angular

  - Add a new component with `ng n c edit-hero --skip-tests` in app\components

<figure>
  <a href="/assets/img/posts/angular_superhero/20.jpg"><img src="/assets/img/posts/angular_superhero/20.jpg"></a>
  <figcaption>Build a Edit-Hero Component with Angular</figcaption>
</figure>

* SuperHero.UI/src/app/components/edit-hero.component.ts
{% highlight ts %}
...
export class EditHeroComponent implements OnInit {
  @Input() hero?: SuperHero;
...
  updateHero(hero:SuperHero){

  }

  deleteHero(hero:SuperHero){
    
  }

  createHero(hero:SuperHero){
    
  }
  ...
{% endhighlight %}

* SuperHero.UI/src/app.module.ts
{% highlight ts %}
...
imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    FormsModule
  ],
...
{% endhighlight %}

* SuperHero.UI/src/app/components/edit-hero.component.ts
{% highlight html %}
<div *ngIf="hero">
    <h2>{{ hero.name }}</h2>
    <div>
        Name:
        <input [(ngModel)]="hero.name" placeholder="Name"/>
    </div>
    <div>
        First Name:
        <input [(ngModel)]="hero.firstName" placeholder="First Name"/>
    </div>
    <div>
        Last Name:
        <input [(ngModel)]="hero.lastName" placeholder="Last Name"/>
    </div>
    <div>
        Place:
        <input [(ngModel)]="hero.place" placeholder="Place"/>
    </div>
    <button (click)="updateHero(hero)" *ngIf="hero.id">Save</button>
    <button (click)="deleteHero(hero)" *ngIf="hero.id">Delete</button>
    <button (click)="createHero(hero)" *ngIf="!hero.id">Create</button>
</div>
{% endhighlight %}

# Add the Edit-Hero Form to the Parent Form

* SuperHero.UI/src/app/app.component.html
{% highlight html %}
<button (click)="initNewHero()">Create New Hero</button>

<table>
    <thead>
        <th>Name</th>
        <th>First Name</th>
        <th>Last Name</th>
        <th>Place</th>
    </thead>
    <tbody>
        <tr *ngFor="let hero of heroes">
            <td>{{ hero.name }}</td>
            <td>{{ hero.firstName }}</td>
            <td>{{ hero.lastName }}</td>
            <td>{{ hero.place }}</td>
            <td><button (click)="editHero(hero)">Edit</button></td>
        </tr>
    </tbody>
</table>

<app-edit-hero [hero]="heroToEdit"></app-edit-hero>
{% endhighlight %}

* SuperHero.UI/src/app/app.component.ts
{% highlight ts %}
export class AppComponent {
  title = 'SuperHero.UI';
  heroes: SuperHero[] = [];
  heroToEdit?: SuperHero;

  constructor(private superHeroService: SuperHeroService) {}

  ngOnInit() : void {
    this.superHeroService
      .getSuperHeroes()
      .subscribe((result: SuperHero[]) => (this.heroes = result));
  }

  initNewHero(){
    this.heroToEdit = new SuperHero();
  }

  editHero(hero: SuperHero){
    this.heroToEdit = hero;
  }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/angular_superhero/21.jpg"><img src="/assets/img/posts/angular_superhero/21.jpg"></a>
  <figcaption>Add the Edit-Hero Form to the Parent Form</figcaption>
</figure>

# Implement Web Service Calls on the client

[Download](https://github.com/leehuhlee/AngularEcommerce){: .btn}