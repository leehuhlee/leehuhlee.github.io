---
layout: post
title: "[ASP.Net] Blazor Server APP"
date: 2021-02-02
excerpt: "Form, Validation"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Code
* Pages\FetchData.razor
{% highlight C# %}
  ...
  <button class="btn btn-primary" @onclick="AddNewForecast">
    Add New Forecast
  </button>

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
          </div>
        </div>
      </div>
    </div>
  }

  @code {
    private List<WeatherForecast> _forecasts;

    bool _showPopup = false;
    WeatherForecast _addForecast;
    ...

    void AddNewForecast()
    {
      _showPopup = true;
      _addForecast = new WeatherForecast();
    }

    void SaveForecast()
    {
      _showPopup = false;
      _addForecast.Date = DateTime.Now;
      _forecasts.Add(_addForecast);
    }

    void ClosePopup()
    {
      _showPopup = false;
    }
  }
{% endhighlight %}

* Data\WeatherForecastService.cs
{% highlight C# %}
  public Task<List<WeatherForecast>> GetForecastAsync(DateTime startDate)
  {
    var rng = new Random();
    return Task.FromResult(Enumerable.Range(1, 5).Select(index => new WeatherForecast
    {
      Date = startDate.AddDays(index),
      TemperatureC = rng.Next(-20, 55),
      Summary = Summaries[rng.Next(Summaries.Length)]
    }).ToList());
  }
{% endhighlight %}

* Data\WearherForecast.cs
{% highlight C# %}
  [Required(ErrorMessage = "NeedTemperatureC!")]
  [Range(typeof(int), "-100", "100")]
  public int TemperatureC { get; set; }

  [Required(ErrorMessage = "Need Summary!")]
  [StringLength(10, MinimumLength = 2, ErrorMessage = "2-10")]
  public string Summary { get; set; }
{% endhighlight %}


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


# Result
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-Form-Validation.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
