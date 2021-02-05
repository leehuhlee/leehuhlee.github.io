---
layout: post
title: "[C#] Delegate"
date: 2021-02-05
excerpt: "Delegate, Chaning"
tags: [C#, Game]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part1/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part1: C# 기초 프로그래밍 입문</a></center>


## Code
{% highlight C# %}
  using System;

  namespace Delegate
  {
    class Program
    {
      delegate int OnClicked();

      static void ButtonPressed(OnClicked clickedFunction)
      {
        clickedFunction();
      }


      static int TestDelegate()
      {
        Console.WriteLine("Hello Delegate!");
        return 0;
      }
      
      static int TestDelegate2()
      {
        Console.WriteLine("Hello Delegate 2!");
        return 0;
      }

      static void Main(string[] args)
      {
        OnClicked clicked = new OnClicked(TestDelegate);
        clicked += TestDelegate2;

        ButtonPressed(clicked);
      }
    }
  }
{% endhighlight %}


## Delegate
* Delegate
  - The function is passed over to the factor
  - It can be chained by combining functions


## Result
<figure>
  <a href="/assets/img/posts/cshap_delegate/0.jpg"><img src="/assets/img/posts/cshap_delegate/0.jpg"></a>
	<figcaption>C# Delegate</figcaption>
</figure>



[Download](https://github.com/leehuhlee/CShap){: .btn}
