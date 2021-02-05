---
layout: post
title: "[ASP.Net] Blazor Server APP"
date: 2021-02-04
excerpt: "JavaScript"
tags: [C#, DotNet, ASP.Net, Blazor, JavaScript]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part6/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part6: 웹 서버</a></center>


## Code
* wwwroot\test.js
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

* Pages\ _Host.cshtml
{% highlight C# %}
  ...
  <script src="test.js"></script>
  ...
{% endhighlight %}

* Pages\Index.razor
{% highlight C# %}
  ...
  <Counter></Counter>
  ...
{% endhighlight %}

* Shared\NavMenu.razor
{% highlight C# %}
  @inject BlazorApp.Data.CounterState CounterState
  @implements IDisposable
  ...
  <li class="nav-item px-3">
    <NavLink class="nav-link" href="JSInterop">
      <span class="oi oi-list-rich" aria-hidden="true"></span> JSInterop
    </NavLink>
  </li>
  ...
{% endhighlight %}

* Pages\JSInterop.razor
{% highlight C# %}
  @page "/JSInterop"
  @inject IJSRuntime JSRuntime

  <h3>JSInterop</h3>
  <div>
    <button type="button" class="btn btn-primary" @onclick="HelloWorld">
      Hello World
    </button>
  </div>

  <br />
  <div>
    <button type="button" class="btn btn-primary" @onclick="InputName">
      Input Name
    </button>
    <p>@_name</p>
  </div>

  @code {

    string _name = "";

    public async void HelloWorld()
    {
      await JSRuntime.InvokeVoidAsync("testFunction.helloWorld", null);
    }

    public async void InputName()
    {
      _name = await JSRuntime.InvokeAsync<string>("testFunction.inputName", "Input Name");

      StateHasChanged();
    }
  }
{% endhighlight %}


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


# Result

## Hello World
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-JavaScript-HelloWorld.mp4" frameborder="0"> </iframe>

## Input Name
<iframe width="560" height="315" src="/assets/video/posts/aspdotnet_blazorapp/BlazorApp-JavaScript-InputName.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
