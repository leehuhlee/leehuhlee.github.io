---
layout: post
title: "C# A* Maze"
date: 2021-01-08
excerpt: "A* and Priority Queue Algorithm"
tags: [C#, Game, Algorithm]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part2/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part2: 자료구조와 알고리즘</a></center>


## Code
* PriorityQueue.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;
  using System.Diagnostics.CodeAnalysis;

  namespace PriorityQueue
  {
    class PriorityQueue<T> where T: IComparable<T>
    {
      List<T> _heap = new List<T>();

      // O(logN)
      public void Push(T data)
      {
        // Add new data on end
        _heap.Add(data);

        int now = _heap.Count - 1;

        // Start
        while (now > 0)
        {
          // Try
          int next = (now - 1) / 2;
          if (_heap[now].CompareTo(_heap[next]) < 0)
            break;

          // Change
          T temp = _heap[now];
          _heap[now] = _heap[next];
          _heap[next] = temp;

          // Change check location
          now = next;
        }
      }

      // O(logN)
      public T Pop()
      {
        // Save return data
        T ret = _heap[0];

        // Move last data to root
        int lastIndex = _heap.Count - 1;
        _heap[0] = _heap[lastIndex];
        _heap.RemoveAt(lastIndex);
        lastIndex--;

        // Reverse
        int now = 0;
        while (true)
        {
          int left = 2 * now + 1;
          int right = 2 * now + 2;

          int next = now;
          // If left is bigger than now, move left
          if (left <= lastIndex && _heap[next].CompareTo(_heap[left]) < 0)
            next = left;
          // If right is bigger than now, move right
          if (right <= lastIndex && _heap[next].CompareTo(_heap[right]) < 0)
            next = right;

          // If left/right is smaller than now, stop
          if (next == now)
            break;

          // change
          T temp = _heap[now];
          _heap[now] = _heap[next];
          _heap[next] = temp;

          // change check location
          now = next;
        }

        return ret;
      }

      public int Count{ get { return _heap.Count; } }
    }
  }
{% endhighlight %}

* Player.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace AStarAlgorithm  
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

		  public void Initialize(int posY, int posX, Board board) 
  		{
	  		PosY = posY;
		  	PosX = posX;
			  _board = board;

  			AStar();
	  	}

		  struct PQNode : IComparable<PQNode>
		  {
		  	public int F;
	  		public int G;
	  		public int Y;
	  		public int X;

		  	public int CompareTo(PQNode other)
		  	{
		  		if (F == other.F)
		  			return 0;
			  	return F < other.F ? 1 : -1;
			  }
  		}

	  	void AStar()
	  	{
	  		// U L D R UL DL DR UR
	  		int[] deltaY = new int[] { -1, 0, 1, 0 };
	  		int[] deltaX = new int[] { 0, -1, 0, 1 };
	  		int[] cost = new int[] { 10, 10, 10, 10 };

	  		// Calculate Score
	  		// F = G + H
	  		// F = Result Score (The Smaller, The better, Depends on path)
	  		// G = Cost to go destination from start (The Smaller, The better, Depends on path)
	  		// H = how close from destination (The Smaller, The better, fixed)

		  	// check (y, x) is closed
	  		bool[,] closed = new bool[_board.Size, _board.Size]; // CloseList

		  	// check way to go (y, x) is found
		  	// found X => MaxValue
		  	// found O => F = G + H
		  	int[,] open = new int[_board.Size, _board.Size]; // OpenList
		  	for (int y = 0; y < _board.Size; y++)
		  		for (int x = 0; x < _board.Size; x++)
		  			open[y, x] = Int32.MaxValue;  

	  		Pos[,] parent = new Pos[_board.Size, _board.Size];

	  		// Among datas in Open List, choose the best nimonee fast
		  	PriorityQueue<PQNode> pq = new PriorityQueue<PQNode>();

		  	// Find Start (open)
	  		open[PosY, PosX] = 10 * (Math.Abs(_board.DestY - PosY) + Math.Abs(_board.DestX - PosX));
	  		pq.Push(new PQNode() { F = 10 * (Math.Abs(_board.DestY - PosY) + Math.Abs(_board.DestX - PosX)), G = 0, Y = PosY, X = PosX });
	  		parent[PosY, PosX] = new Pos(PosY, PosX);

		  	while (pq.Count > 0)
		  	{
		  		// Search the best nimonee
		  		PQNode node = pq.Pop();
		  		// Search same coordinate with several path, if it is closed by more fast path, skip
	  			if (closed[node.Y, node.X])
		  			continue;

			  	// Close
			  	closed[node.Y, node.X] = true;
		  		// Stop when destination
			  	if (node.Y == _board.DestY && node.X == _board.DestX)
			  		break;

			  	// Check coordinate is able to move, and open
			  	for (int i = 0; i < deltaY.Length; i++)
			  	{
			  		int nextY = node.Y + deltaY[i];
			  		int nextX = node.X + deltaX[i]; 

					  // Skip if unvalid range  
			  		if (nextX < 0 || nextX >= _board.Size || nextY < 0 || nextY >= _board.Size)
			  			continue;
		  			// Skip if wall
		  			if (_board.Tile[nextY, nextX] == Board.TileType.Wall)
		  				continue;
		  			// Skip if closed
		  			if (closed[nextY, nextX])
		  				continue;

			  		// Calculate cost
			  		int g = node.G + cost[i];
			  		int h = 10 * (Math.Abs(_board.DestY - nextY) + Math.Abs(_board.DestX - nextX));
			  		// If more fast way is found in other path, skip
			  		if (open[nextY, nextX] < g + h)
			  			continue;

				  	// Open
				  	open[nextY, nextX] = g + h;
				  	pq.Push(new PQNode() { F = g + h, G = g, Y = nextY, X = nextX });
				  	parent[nextY, nextX] = new Pos(node.Y, node.X);
			  	}
			  }

		  	CalcPathFromParent(parent);
		  }

	  	void CalcPathFromParent(Pos[,] parent)
	  	{
	  		int y = _board.DestY;
	  		int x = _board.DestX;
	  		while (parent[y, x].Y != y || parent[y, x].X != x)
	  		{
	  			_points.Add(new Pos(y, x));
	  			Pos pos = parent[y, x];
	  			y = pos.Y;
	  			x = pos.X;
	  		}
	  		_points.Add(new Pos(y, x));
	  		_points.Reverse();
	  	}

	  	const int MOVE_TICK = 30;
	  	int _sumTick = 0;
	  	int _lastIndex = 0;
	  	public void Update(int deltaTick)
	  	{
	  		if (_lastIndex >= _points.Count)
	  		{
	  			_lastIndex = 0;
	  			_points.Clear();
		  		_board.Initialize(_board.Size, this);
		  		Initialize(1, 1, _board);
		  	}

		  	_sumTick += deltaTick;
		  	if (_sumTick >= MOVE_TICK)
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
* Heap
  - Data structure created for priority queue as a type of complete binary tree
  - Data structure that can easily extract maximum and minimum values

* Priority Queue
  - Creates a maximum heap (in the form of a full binary tree) with n elements to be aligned
  - Ejects elements from the heap one at a time and stores them from the back of the array
  - Sort deleted elements (delete from maximum) in the order in which values are reduced


## Interface
* IComparable
  - Make a condition on type
{% highlight java %}
  class PriorityQueue<T> where T: IComparable<T>
{% endhighlight %}
* CompareTo
  - Compare two values and return result<br>
{% highlight java %}
  if (_heap[now].CompareTo(_heap[next]) < 0)
{% endhighlight %}


## Struct
* Structure
  - a composite data type
  - defines a physically grouped list of variables under one name in a block of memory, allowing the different variables to be accessed via a single pointer or by the struct declared name which returns the same address
  - contain other data types so is used for mixed-data-type records such as a hard-drive directory entry (file length, name, extension, physical address, etc.), or other mixed-type records (name, address, telephone, balance, etc.).
{% highlight java %}
   struct PQNode : IComparable<PQNode>
		  {
		  	public int F;
	  		public int G;
	  		public int Y;
	  		public int X;

		  	public int CompareTo(PQNode other)
		  	{
		  		if (F == other.F)
		  			return 0;
			  	return F < other.F ? 1 : -1;
			  }
  		}
{% endhighlight %}

## Result
<iframe width="560" height="315" src="/assets/video/posts/cshap_astar/Cshap-AStar.mp4" frameborder="0"> </iframe>


[Download](https://github.com/leehuhlee/CShap){: .btn}
