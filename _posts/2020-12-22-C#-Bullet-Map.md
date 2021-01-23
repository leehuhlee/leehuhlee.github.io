---
layout: post
title: "C# Bullet Map"
date: 2020-12-22
excerpt: "Color in C#"
tags: [C#, Game]
comments: false
---


<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part2/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part2: 자료구조와 알고리즘</a></center>


## Code
{% highlight C# %}
  using System;

  namespace Bullet_Map
  {
    class Program
    {
      class Map
      {
        int[,] tiles = {
          { 1, 1, 1, 1, 1},
          { 1, 0, 0, 0, 1},
          { 1, 0, 0, 0, 1},
          { 1, 0, 0, 0, 1},
          { 1, 1, 1, 1, 1},
        };

        public void Render()
        {
          ConsoleColor defaultColor = Console.ForegroundColor;

          for (int y = 0; y < tiles.GetLength(1); y++)
          {
            for (int x = 0; x < tiles.GetLength(0); x++)
            {
              if (tiles[y, x] == 1)
                Console.ForegroundColor = ConsoleColor.Red;
              else
                Console.ForegroundColor = ConsoleColor.Green;
              Console.Write('\u25cf');
            }
            Console.WriteLine();
          }

          Console.ForegroundColor = defaultColor;
        }
      }
      
      static void Main(string[] args)
      {
        Map map = new Map();
        map.Render();
      }
    }
  }
{% endhighlight %}


## Multidimensional Arrays
  - An array of more than two dimensions
  - Has another array as an array element
{% highlight C# %}
  int[,] tiles = {
          { 1, 1, 1, 1, 1},
          { 1, 0, 0, 0, 1},
          { 1, 0, 0, 0, 1},
          { 1, 0, 0, 0, 1},
          { 1, 1, 1, 1, 1},
        };
{% endhighlight %}


## ConsoleColor
* ConsoleColor
  - Specifies a constant that defines the foreground and background colors of the console
{% highlight C# %}
  ConsoleColor defaultColor = Console.ForegroundColor;
  ...
  Console.ForegroundColor = ConsoleColor.Red;
{% endhighlight %}
* Console.ForegroundColor
  - Gets or sets the foreground color of the console
{% highlight C# %}
  ConsoleColor defaultColor = Console.ForegroundColor;
{% endhighlight %}

## Result
<figure>
  <a href="/assets/img/posts/cshap_bullet_map/0.jpg"><img src="/assets/img/posts/cshap_bullet_map/0.jpg"></a>
	<figcaption>C# Bullet Map</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
