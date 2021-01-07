---
layout: post
title: "C# Side Winder Maze and Right Hand Rule"
date: 2020-12-26
excerpt: "Side Winder Maze  and Right Hand Rule Algorithm"
tags: [C#, Game, Algorithm]
comments: false
---


<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part2/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part2: 자료구조와 알고리즘</a></center>


## Code
* Program.cs
{% highlight java %}
  using System;

  namespace SideWinderMaze
  {
    class Program
    {
      static void Main(string[] args)
      {
        Board board = new Board();
        Player player = new Player();

        board.Initialize(25, player);
        player.Initionalize(1, 1, board);

        Console.CursorVisible = false;

        const int WAIT_TICK = 1000 / 30;
        int lastTick = 0;

        while (true)
        {
          // Frame
          int currentTick = System.Environment.TickCount;
          if (currentTick - lastTick < WAIT_TICK)
            continue;
          int deltaTick = currentTick - lastTick;
          lastTick = currentTick;

          // Logic
          player.Update(deltaTick);

          // Rendering
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

  namespace SideWinderMaze
  {
    class Board
    {
      const char CIRCLE = '\u25cf';
      public TileType[,] Tile { get; private set; }
      public int Size { get; private set; }

      public int DestY { get; private set; }
      public int DestX { get; private set; }

      Player _player;

      public enum TileType
      {
        Empty,
        Wall,
      }

      public void Initialize(int size, Player player)
      {
        if (size % 2 == 0)
          return;

        _player = player;

        Tile = new TileType[size, size];
        Size = size;

        DestY = Size - 2;
        DestX = Size - 2;

        GeneratedBySideWinder(size);
      }

      public void GeneratedBySideWinder(int size)
      {

        for(int y = 0; y < Size; y++)
        {
          for(int x = 0; x < Size; x++)
          {
            if (x % 2 == 0 || y % 2 == 0)
              Tile[y, x] = TileType.Wall;
            else
              Tile[y, x] = TileType.Empty;
          }
        }

        Random rand = new Random();

        for (int y = 0; y < Size; y++)
        {
          int count = 1;
          for(int x = 0; x < Size; x++)
          {
            if (x % 2 == 0 || y % 2 == 0)
              continue;

            if (x == Size - 2 && y == Size - 2)
              continue;

            if(y == Size - 2)
            {
              Tile[y, x + 1] = TileType.Empty;
              continue;
            }

            if(x == Size - 2)
            {
              Tile[y + 1, x] = TileType.Empty;
              continue;
            }

            if (rand.Next(0, 2) == 0)
            {
              Tile[y, x + 1] = TileType.Empty;
              count++;
            }
            else
            {
              int randomIndex = rand.Next(0, count);
              Tile[y + 1, x - randomIndex * 2] = TileType.Empty;
              count = 1;
            }
          }
        }
      }

      public void Render()
      {
        ConsoleColor prevColor = Console.ForegroundColor;

        for(int y = 0; y < Size; y++)
        {
          for(int x = 0; x < Size; x++)
          {
            // Get player position
            // If current [y, x] == player position, change color
            if (y == _player.PosY && x == _player.PosX)
              Console.ForegroundColor = ConsoleColor.Blue;
            else if (y == DestY && x == DestX)
              Console.ForegroundColor = ConsoleColor.Yellow;
            else
              Console.ForegroundColor = GetTileColor(Tile[y, x]);

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

* Player.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace SideWinderMaze
  {
    class Pos
    {
      public Pos(int y, int x) { Y = y; X = x; }
      public int Y;
      public int X;
    }

    class Player
    {
      public int PosY { get; private set; }
      public int PosX { get; private set; }
      Random _random = new Random();
      Board _board;

      enum Dir
      {
        Up = 0,
        Left = 1,
        Down = 2,
        Right = 3
      }

      int _dir = (int)Dir.Up;
      List<Pos> _points = new List<Pos>();

      public void Initionalize(int posY, int posX, Board board)
      {
        PosY = posY;
        PosX = posX;

        _board = board;

        // Position changing from current direction
        int[] frontY = new int[] { -1, 0, 1, 0 };
        int[] frontX = new int[] { 0, -1, 0, 1 };
        int[] rightY = new int[] { 0, -1, 0, 1 };
        int[] rightX = new int[] { 1, 0, -1, 0 };

        _points.Add(new Pos(PosY, PosX));

        // Not arrived to destination
        while (PosY != board.DestY || PosX != board.DestX)
        {
          // 1. Check able to go right 
          if (_board.Tile[PosY + rightY[_dir], PosX + rightX[_dir]] == Board.TileType.Empty)
          {
            // Turn right 90 degree
            _dir = (_dir - 1 + 4) % 4;
            // Go straight 
            PosY = PosY + frontY[_dir];
            PosX = PosX + frontX[_dir]; 
            _points.Add(new Pos(PosY, PosX));
          }
          // 2. Check albe to go straight
          else if (_board.Tile[PosY + frontY[_dir], PosX + frontX[_dir]] == Board.TileType.Empty)
          {
            // Go straight
            PosY = PosY + frontY[_dir];
            PosX = PosX + frontX[_dir];
            _points.Add(new Pos(PosY, PosX));
          }
          else
          {
            // Turn left 90 degree
            _dir = (_dir + 1 + 4) % 4;
          }
        }
      }

      const int MOVE_TICK = 10;
      int _sumTick = 0;
      int _lastIndex = 0;

      public void Update(int deltaTick)
      {
        if (_lastIndex >= _points.Count)
          return;

        _sumTick += deltaTick;
        if(_sumTick >= MOVE_TICK)
        {
          _sumTick = 0;

          PosY = _points[_lastIndex].Y;
          PosX = _points[_lastIndex].X;
          _lastIndex++;
                
        }
      }
    }
  }
{% endhighlight %}

## Algorithm
* Initialize
  - Make first row and column and last row and column to wall
  - Make every enum-th row and column to wall
  
* Side Winder Maze
  - Change next row of enum-th to empty randomly
  - If next random is wall, choose one of previous empty block
  - Make that empty block's bottom to empty

* Right Hand Rule
  - In current direction, go right block if it is empty
  - In current direction, go straight if it is empty
  - Otherwise, turn left


## Timer
* WAIT_TICK
  - Excute while loop in every 1000/30 ms
* Calculate
  - currentTick: System Timer
  - lasTick: last checked Time
  - deltaTick: duration of Tick
{% highlight java %}
  const int WAIT_TICK = 1000 / 30;
  int lastTick = 0;

  while (true)
  {
    // Frame
    int currentTick = System.Environment.TickCount;
    if (currentTick - lastTick < WAIT_TICK)
      continue;
    int deltaTick = currentTick - lastTick;
    lastTick = currentTick;

    // Logic
    player.Update(deltaTick);
{% endhighlight %}
* Move_TICK
  - Move player in every Move_TICK
{% highlight java %}
  _sumTick += deltaTick;
  if(_sumTick >= MOVE_TICK)
  {
    _sumTick = 0;
    ...
{% endhighlight %}


## Property
* Getter
  - Return value
* Setter
  - Change value
  - If value should not be changed in other classes, use private set
{% highlight java %}
  public TileType[,] Tile { get; private set; }
{% endhighlight %}


## List
* Dynamical Array
  - Allocate memory 1.5 - 2 times more then real using memory to minimize cost of resizing
  - Pros: flexible and continuous
  - Cos: cost of resizing
{% highlight java %}
  List<Pos> _points = new List<Pos>();
  ...
  _points.Add(new Pos(PosY, PosX));
  ...
  PosY = _points[_lastIndex].Y;
{% endhighlight %}


## Generic
* List<T>
  - To facilitate the reuse of code and algorithms
  - Specifies types when classes are used instead of when designing existing classes
{% highlight java %}
  List<Pos> _points = new List<Pos>();
{% endhighlight %}


## Calculate rotation
* Set direction to number
{% highlight java %}
  enum Dir
  {
    Up = 0,
    Left = 1,
    Down = 2,
    Right = 3
  }
{% endhighlight %}
* Turn Right
  - For example, if player face up, then player direction must be changed to right
  - 0 -> 3, 1 -> 0, 2 -> 1, 3 -> 2
  - +4 is for positive number
{% highlight java %}
  _dir = (_dir - 1 + 4) % 4;
{% endhighlight %}
* Turn Left
  - For example, if player face up, then player direction must be changed to left
  - 0 -> 1, 1 -> 2, 2 -> 3, 3 -> 0
{% highlight java %}
  _dir = (_dir + 1 + 4) % 4;
{% endhighlight %}


## Calculate position
* Front direction
  - If player faces up, decrese row
  - If player faces down, increse row 
{% highlight java %}
  int[] frontY = new int[] { -1, 0, 1, 0 };
{% endhighlight %}
  - If player faces left, decrese column
  - If player faces right, increse column 
{% highlight java %}
  int[] frontX = new int[] { 0, -1, 0, 1 };
{% endhighlight %}
* Right direction
  - If player faces left, decrese row
  - If player faces right, increse row 
{% highlight java %}
  int[] rightY = new int[] { 0, -1, 0, 1 };
{% endhighlight %}
  - If player faces up, increase column
  - If player faces down, decrease coloum
{% highlight java %}
  int[] rightX = new int[] { 1, 0, -1, 0 };
{% endhighlight %}


## Result
<figure class = "third">
  <a href="/assets/img/posts/cshap_sidewindermaze/0.jpg"><img src="/assets/img/posts/cshap_sidewindermaze/0.jpg"></a>
  <a href="/assets/img/posts/cshap_sidewindermaze/1.jpg"><img src="/assets/img/posts/cshap_sidewindermaze/1.jpg"></a>
  <a href="/assets/img/posts/cshap_sidewindermaze/2.jpg"><img src="/assets/img/posts/cshap_sidewindermaze/2.jpg"></a>
	<figcaption>C# Side Winder Maze</figcaption>
</figure>

## Demo Video
<iframe width="560" height="315" src="/assets/video/posts/cshap_sidewindermaze/Cshap-Side-Winder-Maze.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/CShap){: .btn}
