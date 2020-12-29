---
layout: post
title: "C# Rock, Scissors, Paper"
date: 2020-12-17
excerpt: "Random and ReadLine"
tags: [C#, Game]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/CShap"><b>this C# codes</b></a> in github.</center>


## Code
{% highlight java %}
  using System;

  namespace RockScissorsPaper
  {
    enum Choice
    {
      Rock = 0,
      Scissors = 1,
      Paper = 2
    }

    class Program
    {
      static void Main(string[] args)
      {
        Random rand = new Random();
        int aiChoice = rand.Next(0, 3);
        int choice = Convert.ToInt32(Console.ReadLine());

        switch (choice)
        {
          case (int)Choice.Rock:
            Console.WriteLine("Your choice is rock.");
            break;
          case (int)Choice.Scissors:
            Console.WriteLine("Your choice is scissors.");
            break;
          case (int)Choice.Paper:
            Console.WriteLine("Your choice is paper.");
            break;
        }

        switch (aiChoice)
        {
          case (int)Choice.Rock:
            Console.WriteLine("AI choice is rock.");
              break;
          case (int)Choice.Scissors:
            Console.WriteLine("AI choice is scissors.");
            break;
          case (int)Choice.Paper:
            Console.WriteLine("AI choice is paper.");
            break;
        }

        if(choice == (int)Choice.Rock)
        {
          if(aiChoice == (int)Choice.Rock)
          {
            Console.WriteLine("Draw :|");
          }
          else if(aiChoice == (int)Choice.Scissors)
          {
            Console.WriteLine("Win :)");
          }
          else
          {
            Console.WriteLine("Lose :(");
          }
        }
        else if(choice == (int)Choice.Scissors)
        {
          if (aiChoice == (int)Choice.Rock)
          {
            Console.WriteLine("Lose :(");
          }
          else if (aiChoice == (int)Choice.Scissors)
          {
            Console.WriteLine("Draw :|");
          }
          else
          {
            Console.WriteLine("Win :)");
          }
        }
        else
        {
          if (aiChoice == (int)Choice.Rock)
          {
            Console.WriteLine("Win :)");
          }
          else if (aiChoice == (int)Choice.Scissors)
          {
            Console.WriteLine("Lose :(");
          }
          else
          {
            Console.WriteLine("Draw :|");
          }
        }
      }
    }
  }
{% endhighlight %}


## Static
* static
  - Can be used without invoking an instance
  - Stack Memory
{% highlight java %}
  static void Main(string[] args)
{% endhighlight %}


## new className()
* new Operator
  - Create an object and call a constructor
  - Heap Memory
{% highlight java %}
  Random rand = new Random();
{% endhighlight %}


## Random
* Exist in System Namespace
* Random Constructor
  - Use `new` keyword and the constructor to create Random type object
{% highlight java %}
  Random rand = new Random();
{% endhighlight %}


## Next() Method
* Next(int minValue, int maxValue)
  - minValue ~ maxValue-1
  - Return Integer(n>=0)
* Next(int maxValue)
  - 0 ~ maxValue-1



## Type Casting
* String -> Int
  - Convert.ToInt32(String string)
  - If string is null, then 0
{% highlight java %}
  int choice = Convert.ToInt32(Console.ReadLine());
{% endhighlight %}

* enum -> int
  - (Type)enumClass.enumObject
  - Be Careful when you change type bigger to small
{% highlight java %}
  switch (choice)
  {
    case (int)Choice.Rock:
      Console.WriteLine("Your choice is rock.");
      break;
    case (int)Choice.Scissors:
      Console.WriteLine("Your choice is scissors.");
      break;
    case (int)Choice.Paper:
      Console.WriteLine("Your choice is paper.");
      break;
  }
{% endhighlight %}


## Console.ReadLine()
* Input string from console
{% highlight java %}
  int choice = Convert.ToInt32(Console.ReadLine());
{% endhighlight %}


## Console.WriteLine()
* Print string on console
{% highlight java %}
  Console.WriteLine("Your choice is rock.");
{% endhighlight %}


## Result
<figure class="third">
  <a href="/assets/img/posts/cshap_rockscissorspaper/0.jpg"><img src="/assets/img/posts/cshap_rockscissorspaper/0.jpg"></a>
  <a href="/assets/img/posts/cshap_rockscissorspaper/1.jpg"><img src="/assets/img/posts/cshap_rockscissorspaper/1.jpg"></a>
  <a href="/assets/img/posts/cshap_rockscissorspaper/2.jpg"><img src="/assets/img/posts/cshap_rockscissorspaper/2.jpg"></a>
	<figcaption>C# Rock, Scissors, Paper</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/cshap_rockscissorspaper/Cshap-Rock-Scissors-Paper.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
