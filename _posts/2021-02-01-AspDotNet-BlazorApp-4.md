---
layout: post
title: "[ASP.Net] Blazor Server App(4)"
date: 2021-02-01
excerpt: "Templated Component"
tags: [C#, DotNet, ASP.Net, Blazor]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Code
* Pages\FetchData.razor
{% highlight C# %}
  @page "/fetchdata"

  @using BlazorApp.Data
  @inject WeatherForecastService ForecastService

  <h1>Weather forecast</h1>

  <p>This component demonstrates fetching data from a service.</p>

  @if (forecasts == null)
  {
    <p><em>Loading...</em></p>
  }
  else
  {
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
  }

  @code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
      forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
    }
  }

{% endhighlight %}

* Pages\ShowUser.razor
{% highlight C# %}
  @using BlazorApp.Data

  @typeparam TItem

  <h3>TableTemplate</h3>

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

  @code {
    [Parameter]
    public RenderFragment Header { get; set; }

    [Parameter]
    public RenderFragment<TItem> Row { get; set; }

    [Parameter]
    public IReadOnlyList<TItem> Items { get; set; }
  }
{% endhighlight %}


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


## Result
<figure>
  <a href="/assets/img/posts/aspdotnet_blazorapp/2.jpg"><img src="/assets/img/posts/aspdotnet_blazorapp/2.jpg"></a>
	<figcaption>ASP.NET Blazor App</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
