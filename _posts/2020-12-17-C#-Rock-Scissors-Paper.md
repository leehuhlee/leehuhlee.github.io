---
layout: post
title: "C# Rock, Scissors, Paper"
date: 2020-12-17
excerpt: "Random and ReadLine"
tags: [C#, Game]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/CShap"><b>this C# games</b></a> in github.</center>


## Code
{% highlight java %}
  using System;

  namespace RockScissorsPaper
  {
    class Program
    {
      static void Main(string[] args)
      {
        // 0: Rock 1: Scissors 2: Paper
        Random rand = new Random();
        int aiChoice = rand.Next(0, 3); // 0 ~ 2
        int choice = Convert.ToInt32(Console.ReadLine());

        switch (choice)
        {
          case 0:
            Console.WriteLine("Your choice is rock.");
            break;
          case 1:
            Console.WriteLine("Your choice is scissors.");
            break;
          case 2:
            Console.WriteLine("Your choice is paper.");
            break;
        }

        switch (aiChoice)
        {
          case 0:
            Console.WriteLine("AI choice is rock.");
            break;
          case 1:
            Console.WriteLine("AI choice is scissors.");
            break;
          case 2:
            Console.WriteLine("AI choice is paper.");
            break;
        }

        //Result of Game
        if(choice == 0)
        {
          if(aiChoice == 0)
          {
            Console.WriteLine("Draw :|");
          }
          else if(aiChoice == 1)
          {
            Console.WriteLine("Win :)");
          }
          else
          {
            Console.WriteLine("Lose :(");
          }
        }
        else if(choice == 1)
        {
          if (aiChoice == 0)
          {
            Console.WriteLine("Lose :(");
          }
          else if (aiChoice == 1)
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
          if (aiChoice == 0)
          {
            Console.WriteLine("Win :)");
          }
          else if (aiChoice == 1)
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


## Random
{% highlight java %}
  Random rand = new Random();
  int aiChoice = rand.Next(0,3);
{% endhighlight %}
* Exist in System Namespace
* Random Generator
  - Use `new` keyword and Generator to create Random type object


## Next() Method
- Return Integer(n>=0)
* Next(int minValue, int maxValue)
  - minValue ~ maxValue-1
* Next(int maxValue)
  - 0 ~ maxValue-1


## Result
<figure class="third">
  <a href="/assets/img/posts/cshap_rockscissorspaper/1.jpg"><img src="/assets/img/posts/cshap_rockscissorspaper/1.jpg"></a>
  <a href="/assets/img/posts/cshap_rockscissorspaper/2.jpg"><img src="/assets/img/posts/cshap_rockscissorspaper/2.jpg"></a>
  <a href="/assets/img/posts/cshap_rockscissorspaper/3.jpg"><img src="/assets/img/posts/cshap_rockscissorspaper/3.jpg"></a>
	<figcaption>C# Rock, Scissors, Paper</figcaption>
</figure>


## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/cshap_rockscissorspaper/Cshap-Rock-Scissors-Paper.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
