---
layout: post
title: "[ASP.Net] Blazor Server App"
date: 2021-01-30
excerpt: "Binding, Parameter, ref, EventCallBack, Cascading Parameter, Templated Component, Dependency Injection, Form, Validation, State Management, JavaScript"
tags: [C#, DotNet, ASP.Net, Blazor, JavaScript]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Blazor
* SPA
  - Single Page Application
  - Load HTML and extra files only one time
  - After first Loading, request by Signal(ex. AJAX) with no-reloading
<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/0.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/0.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>

* .cshtml and .razor
  - .cshtml: Razor page file
  - .razor: Razor component file

## Route
* @page
  - define how to match urls
  - method is same with Razor pages
{% highlight C# %}
  @page "/user"
{% endhighlight %}


## Binding
* @
  - specifies to use C#
  - You can make attribute or condition by this, also
{% highlight C# %}
  button class="@_btnClass"
  disabled="@(_users.Count()>=5)
{% endhighlight %}

* @key
  - The @key directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value. 
{% highlight C# %}
  <li @key="user" class="list-group-item">
{% endhighlight %}

* @bind-value
  - bind value by variable name
{% highlight C# %}
  @bind-value="_inputName"
{% endhighlight %}

* @code
  - C# code in razor file
  - seems like script in html file


## Lambda
* ()=>
  - is used for onclick method syntax
{% highlight C# %}
  @onclick="(() => KickUser(user))"
{% endhighlight %}


## System.Threading
* System.Threading Namespace
  - Provides classes and interfaces that enable multithreaded programming. 
  - In addition to classes for synchronizing thread activities and access to data (Mutex, Monitor, Interlocked, AutoResetEvent, and so on), this namespace includes a ThreadPool class that allows you to use a pool of system-supplied threads, and a Timer class that executes callback methods on thread pool threads.
* Timer Class
  - Provides a mechanism for executing a method on a thread pool thread at specified intervals. 
  - This class cannot be inherited.
* InvokeAsync(Action)
  - Executes the specified Action asynchronously on the thread the Dispatcher is associated with.
* StateHasChanged
  - Notifies the component that its state has changed. 
  - When applicable, this will cause the component to be re-rendered.
{% highlight C# %}
  @using System.Threading;
  void AutoIncrement()
  {
    var timer = new Timer(x =>
    {
      InvokeAsync(() =>
      {
        IncrementCount();
        StateHasChanged();
      });
    }, null, 1000, 1000);
  }
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Create.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Delete.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-NotMore5.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Counter.mp4" frameborder="0"> </iframe>

## Parameter
  - In this post, `ShowUser.cshtml` is child and `User.cshtml` is parent
  - To use parent's variable or function in child
* Parent
  - link the variable with child's parameter name 
{% highlight C# %}
  <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
{% endhighlight %}

* Child
  - define variable and spedify it is a parameter by `[Parameter]`
{% highlight C# %}
  [Parameter]
  public List<UserData> Users { get; set; }
{% endhighlight %}


## ref
  - In this post, `ShowUser.cshtml` is child and `User.cshtml` is parent
  - To use child's variable or function in parent
* Parent
  - define variable with child class name
{% highlight C# %}
  <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
  ShowUser _showUser;
{% endhighlight %}


## EventCallBack
* EventCallback struckt
  - A bound event handler delegate
{% highlight C# %}
  public EventCallback CallbackTest { get; set; }
{% endhighlight %}

* EventCallback.InvokeAsync Method
  - Invokes the delegate associated with this binding and dispatches an event notification to the appropriate component.
{% highlight C# %}
  public void KickUser(UserData user)
  {
    Users.Remove(user);
    CallbackTest.InvokeAsync(null);
  }
{% endhighlight %}

* Diffence with `Action`
  - Prefer the strongly typed EventCallback<T>, which provides better error feedback to users of the component. 
  - Similar to other UI event handlers, specifying the event parameter is optional. Use EventCallback when there's no value passed to the callback.
{% highlight C# %}
  public Action CallbackTest { get; set; }
  public void KickUser(UserData user)
  {
    Users.Remove(user);
    CallbackTest.Invoke();
    StateHasChanged();
  }
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Parameter-Ref-EventCallback.mp4" frameborder="0"> </iframe>

## Cascading Parameter
* CascadingValue
  - You can assign parameter for children
  - be applied in children's children
* parent
  - define `Name` to match with child
  - specify variable in `Value` to use parameter
{% highlight C# %}
  <CascadingValue Name="ThemeColor" Value="_selectedColor">
    <ShowUser Users="_users" CallbackTest="CallbackTestFunc" @ref="_showUser"></ShowUser>
  </CascadingValue>

  string _selectedColor = "Green";
{% endhighlight %}

* child
  - link parameter by `Name`
{% highlight C# %}
  [CascadingParameter(Name = "ThemeColor")]
  string _color { get; set; }
{% endhighlight %}


## Select Binding
* Select Binding
  - bind `@bind` expression and `value` attribute
{% highlight C# %}
  <select class="form-control" @bind="_selectedColor">
    @foreach(var option in _options)
    {
      <option value="@option">
        @option
      </option>
    }
  </select>
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Cascading-Parameter.mp4" frameborder="0"> </iframe>

## Templated Component
* Templated Component
  - Razor Component constructed with `RenderFragment`
* RenderFragment Delegate
  - Represents a segment of UI content, implemented as a delegate that writes the content to a RenderTreeBuilder.
* Template
  - You can use your parameters
  - You can pass data with `Context`
{% highlight C# %}
  <TableTemplate Items="forecasts" TItem="WeatherForecast">
    <Header>
      <th>Date</th>
      <th>Temp. (C)</th>
      <th>Temp. (F)</th>
      <th>Summary</th>
    </Header>
    <Row Context="forecast">
      <td>@forecast.Date.ToShortDateString()</td>
      <td>@forecast.TemperatureC</td>
      <td>@forecast.TemperatureF</td>
      <td>@forecast.Summary</td>
    </Row>
  </TableTemplate>

  private WeatherForecast[] forecasts;
{% endhighlight %}
* Usage
  - IReadOnlyList<T> Interface: Represents a read-only collection of elements that can be accessed by index.
  - You can make Generic Type in razor with `@typeparam`
{% highlight C# %}
  @typeparam TItem

  <table class="table">
    <thead>
      <tr>
        @Header
      </tr>
    </thead>
    <tbody>
      @foreach (var item in Items)
      {
        <tr>
          @Row(item)
        </tr>
      }
    </tbody>
  </table>

  [Parameter]
  public RenderFragment Header { get; set; }

  [Parameter]
  public RenderFragment<TItem> Row { get; set; }

  [Parameter]
  public IReadOnlyList<TItem> Items { get; set; }
{% endhighlight %}


## Partial
* Partial
  - If you want to separate razor and code, make class file for code by same name with razor 
  - input `partial` between `public` and `class`
{% highlight C# %}
  public partial class FetchData{...}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/2.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/2.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>


## Dependency Injection
* Dependency
  - In OOP, the lower the dependence, the better
  - Rather than entering the class directly into the code, injecting dependencies using Startup.cs can reduce dependencies
* cs
  - Create Interface
  - Create Class that inherit the interface
{% highlight C# %}
  public interface IFoodService
  {
    IEnumerable<Food> GetFoods();
  }

  public class FoodService : IFoodService
  {
    public IEnumerable<Food> GetFoods()
    {
      List<Food> foods = new List<Food>()
      {
        new Food(){Name = "Bibimbap", Price=7000},
        new Food(){Name = "Kimbap", Price=3000},
        new Food(){Name = "Bossam", Price=9000}
      };

      return foods;
    }
  }
{% endhighlight %}

* Startup.cs
  - Start service in `ConfigureServices`
{% highlight C# %}
  public void ConfigureServices(IServiceCollection services)
  {
    services.AddSingleton<IFoodService, FastFoodService>();
  }
{% endhighlight %}  

* cshtml
  - Inject the interface to use
{% highlight C# %}
  @inject IFoodService foodService
{% endhighlight %}   

* Difference with C++
  - When you use same interfacein different class, you don't need to service the interface again
  - Asp.Net will connect it atomatically
{% highlight C# %}
  public class PaymentService
  {
    IFoodService _service;

    public PaymentService(IFoodService service)
    {
      _service = service;
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddSingleton<PaymentService>();
{% endhighlight %} 

{% highlight C# %}
  @inject PaymentService  paymentService
{% endhighlight %} 

<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/3.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/3.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>

# Service lifetimes

## Singleton
  - Update every time server restart
{% highlight C# %}
  public class SingletonService : IDisposable
  {
    public Guid ID { get; set; }
    public SingletonService()
    {
      ID = Guid.NewGuid();
    }

    public void Dispose()
    {
      Console.WriteLine("SingletonService Disposed");
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddSingleton<SingletonService>();
{% endhighlight %} 

{% highlight C# %}
  @inject SingletonService singleton

  <h1>Singleton</h1>
  Guid: @singleton.ID
{% endhighlight %} 

<figure class="half">
  <a href="/assets/img/posts/aspdotnet_blazorapp/4.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/4.jpg"></a>
  <a href="/assets/img/posts/aspdotnet_blazorapp/5.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/5.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>


## Transient
  - Update every time the page is requested
{% highlight C# %}
  public class TransientService : IDisposable
  {
    public Guid ID { get; set; }
    public TransientService()
    {
      ID = Guid.NewGuid();
    }

    public void Dispose()
    {
      Console.WriteLine("SingletonService Disposed");
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddTransient<TransientService>();
{% endhighlight %} 

{% highlight C# %}
  @inject TransientService transient

  <h1>Transient</h1>
  Guid: @transient.ID
{% endhighlight %} 

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Transient.mp4" frameborder="0"> </iframe>

## Scoped
  - Update every time web connect
{% highlight C# %}
  public class ScopeService : IDisposable
  {
    public Guid ID { get; set; }
    public ScopeService()
    {
      ID = Guid.NewGuid();
    }

    public void Dispose()
    {
      Console.WriteLine("SingletonService Disposed");
    }
  }
{% endhighlight %} 

{% highlight C# %}
  services.AddScoped<ScopeService>();
{% endhighlight %} 

{% highlight C# %}
  @inject ScopeService scoped
  
  <h1>Scoped</h1>
  Guid: @scoped.ID
{% endhighlight %} 

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Scoped.mp4" frameborder="0"> </iframe>

# Form

## Array -> List
* WeatherForecastService.cs
  - To add new object, change Array to List
{% highlight C# %}
  public Task<List<WeatherForecast>> GetForecastAsync(DateTime startDate)
  {
    ...
    }).ToList());
  }
{% endhighlight %}

{% highlight C# %}
  private List<WeatherForecast> _forecasts;
{% endhighlight %}

## Modal
* Modal
  - To use popup in page
  - Use `if-else` like modal-switch
  - For UI, just use specified classes in Bootstrap
{% highlight C# %}
  if (_showPopup)
  {
    <div class="modal" style="display:block" role="dialog">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h3 class="modal-title">Add Forecast</h3>
            <button type="button" class="close" @onclick="ClosePopup">
              <span area-hidden="true">X</span>
            </button>
          </div>
          <div class="modal-body">
            ...
          </div>
        </div>
      </div>
    </div>
  }
  
  bool _showPopup = false;
  WeatherForecast _addForecast;

  void AddNewForecast()
  {
    _showPopup = true;
    ...
  }

  void ClosePopup()
  {
    _showPopup = false;
  }
{% endhighlight %}

# Validation

## class
* [Required]
  - Specify variable must be not null
  - You can put error message in `ErrorMessage`
* [Range]
  - Specify range of variable
  - You can define type in `typeof`, maximum, and minimum
* [StringLength]
  - Specify length of string
  - You can define length of maximum and mininum and put error message in `ErrorMessage`
{% highlight C# %}
  [Required(ErrorMessage = "NeedTemperatureC!")]
  [Range(typeof(int), "-100", "100")]
  public int TemperatureC { get; set; }

  [Required(ErrorMessage = "Need Summary!")]
  [StringLength(10, MinimumLength = 2, ErrorMessage = "2-10")]
  public string Summary { get; set; }
{% endhighlight %}


## Razor Component
* EditForm
  - To make Form
  - Bind value in `Model`
  - Bind function in `OnValidSubmit` after pressing button that type is `submit`
* DataAnnotationsValidator
  - Add Data Annotations Validation
* ValidationSummary 
  - Show ErrorMessage
* InputNumber
  - Make `<input>` tag with validation
  - Type is `int`
  - Bind value in `@bind-value`
* InputText
  - Make `<input>` tag with validation
  - Type is `string`
  - Bind value in `@bind-value`
{% highlight C# %}
  <EditForm Model="_addForecast" OnValidSubmit="SaveForecast">
    <DataAnnotationsValidator />
    <ValidationSummary />
    <label for="TemperatureC">TemperatureC</label>
    <InputNumber class="form-control" placeholder="TemperatureC" @bind-Value="_addForecast.TemperatureC" />
    <label for="Summary">Summary</label>
    <InputText class="form-control" placeholder="Summary" @bind-Value="_addForecast.Summary" />
    <br />
    <button class="btn btn-primary" type="submit">Save</button>
  </EditForm>
{% endhighlight %}

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Form-Validation.mp4" frameborder="0"> </iframe>

## Action 
* Action Delegate
  - Encapsulates a method that has no parameters and does not return a value
  - You can change UI State by Invoke()
{% highlight C# %}
  public Action OnStateChanged;
{% endhighlight %}

## Value
* value
  - The value keyword is used to define the value being assigned by the set accessor.
{% highlight C# %}
  public int Count
  {
    get
    {
      return _count;
    }

    set
    {
      _count = value;
      Refresh();
    }
  }
{% endhighlight %}


## IDisposable
* Idisposable Interface
  - Provides a mechanism for releasing unmanaged resources
* Dispose()
  - Performs application-defined tasks associated with freeing, releasing, or resetting unmanaged resources
   
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-State-Management.mp4" frameborder="0"> </iframe>

## JavaScript
* Function
  - To make a function, at first, you need to define variable
  - define where you put your function. For example, window, document, and kind of tags
  - define function's name and contents like lambda function
  - Call the function
{% highlight java %}
  var func = (function () {
    window.testFunction = {
      helloWorld: function () {
        return alert('Hello World');
      },
      inputName: function (text) {
        return prompt(text, 'Input Name');
      }
    };
  });

  func();
{% endhighlight %}

* Script Binding
  - You can link your script in `_Host.cshtml` by `<sciprt>` tag
{% highlight C# %}
  <script src="test.js"></script>
{% endhighlight %}


## IJSRuntime
* IJSRuntime Interface
  - Represents an instance of a JavaScript runtime to which calls may be dispatched
{% highlight C# %}
  @inject IJSRuntime JSRuntime
{% endhighlight %}

* InvokeAsync<TValue>
  - Invokes the specified JavaScript function asynchronously.
  - Microsoft.JSInterop.JSRuntime will apply timeouts to this operation based on the value configured in Microsoft.JSInterop.JSRuntime.DefaultAsyncTimeout. 
  - To dispatch a call with a different timeout, or no timeout, consider using Microsoft.JSInterop.IJSRuntime.InvokeAsync
  - An identifier for the function to invoke. For example, the value "someScope.someFunction" will invoke the function window.someScope.someFunction
{% highlight C# %}
  _name = await JSRuntime.InvokeAsync<string>("testFunction.inputName", "Input Name");
{% endhighlight %}

* InvokeVoidAsync
  - Invokes the specified JavaScript function asynchronously
  - An identifier for the function to invoke. For example, the value "someScope.someFunction" will invoke the function window.someScope.someFunction

<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-JavaScript-HelloWorld.mp4" frameborder="0"> </iframe>
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-JavaScript-InputName.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
