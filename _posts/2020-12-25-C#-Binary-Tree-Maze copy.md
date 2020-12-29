---
layout: post
title: "C# Binary Tree Maze"
date: 2020-12-25
excerpt: "Binary Tree Maze Algorithm"
tags: [C#, Game, Algorithm]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/CShap"><b>this C# codes</b></a> in github.</center>


## Code
* Program.cs
{% highlight java %}
  using System;

  namespace Maze
  {
    class Program
    {
      static void Main(string[] args)
      {
        Board board = new Board();
        board.GeneratedByBinaryTree(25);

        Console.CursorVisible = false;

        while (true)
        {
          Console.SetCursorPosition(0, 0);
          board.Render();
        }
      }
    }
  }
{% endhighlight %}

* Board.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace Maze
  {
    class Board
    {
      const char CIRCLE = '\u25cf';
    
      public TileType[,] _tile;
      public int _size;
        
      public enum TileType
      {
        Empty,
        Wall,
      }

      public void GeneratedByBinaryTree(int size)
      {
        if (size % 2 == 0)
        return;

        _tile = new TileType[size, size];
        _size = size;

        for(int y = 0; y < _size; y++)
        {
          for(int x = 0; x < _size; x++)
          {
            // Make wall when x or y is enum
            if (x%2==0 || y%2==0)
              _tile[y, x] = TileType.Wall;

            else
               _tile[y, x] = TileType.Empty;
          }
        }

        Random rand = new Random();

        for (int y = 0; y < _size; y++)
        {
          for (int x = 0; x < _size; x++)
          {
            // Pass in wall
            if (x % 2 == 0 || y % 2 == 0)
              continue;

            // Pass in [size - 2, size - 2]
            if (x == _size - 2 && y == _size - 2)
              continue;

            // Change only right direction
            if(y == _size - 2)
            {
              _tile[y, x + 1] = TileType.Empty;
              continue;
            }

            // Change only bottom direction
            if (x == _size - 2)
            {
              _tile[y + 1, x] = TileType.Empty;
              continue;
            }

            if (rand.Next(0, 2) == 0)
              _tile[y, x + 1] = TileType.Empty;
            else
              _tile[y + 1, x] = TileType.Empty;

          }
        }
      }

      public void Render()
      {
        ConsoleColor prevColor = Console.ForegroundColor;

        for(int y = 0; y < _size; y++)
        {
          for(int x = 0; x < _size; x++)
          {
            Console.ForegroundColor = GetTileColor(_tile[y, x]);
            Console.Write(CIRCLE);
          }
          Console.WriteLine();
        }
      }

      ConsoleColor GetTileColor(TileType type)
      {
        switch (type)
        {
          case TileType.Empty:
            return ConsoleColor.Green;
          case TileType.Wall:
            return ConsoleColor.Red;
          default:
            return ConsoleColor.Green;
        }
      }
    }
  }
{% endhighlight %}

## Algorithm
* Initialize
  - Make first row and column and last row and column to wall
  - Make every enum-th row and column to wall
  
* Binary Tree Maze
  - Change next row and column of enum-th to empty randomly
  - If 2nd last row, change only right direction
  - If 2nd last column, change only bottom direction


## Console
* CursorVisible
  - Can show/hide cursor
{% highlight java %}
  Console.CursorVisible = false;
{% endhighlight %}
* SetCursorPosition
  - Set position of cursor with number;
{% highlight java %}
  Console.SetCursorPosition(0, 0);
{% endhighlight %}

## Result
<figure>
  <a href="/assets/img/posts/cshap_binarytreemaze/0.jpg"><img src="/assets/img/posts/cshap_binarytreemaze/0.jpg"></a>
	<figcaption>C# Binary Tree Maze</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
