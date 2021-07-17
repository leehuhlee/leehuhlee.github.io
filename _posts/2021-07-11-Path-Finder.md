---
layout: post
title: "[C#] Path Finder"
date: 2021-07-11
excerpt: "KATA Path Finder Series"
tags: [C#, Maze, Algorithm]
comments: false
---

<center>Reference by <a href="https://www.codewars.com/kata/5765870e190b1472ec0022a2"></a></center>

# Path Finder #1: can you reach the exit?

## Task
You are at position [0, 0] in maze NxN and you can only move in one of the four cardinal directions (i.e. North, East, South, West). Return true if you can reach position [N-1, N-1] or false otherwise.
- Empty positions are marked `.`.
- Walls are marked `W`.
- Start and exit positions are empty in all test cases.

## Solution
{% highlight C# %}
public class Finder
{
    public class Pos
    {
        public int x;
        public int y;
        public Pos(int x, int y) { this.x = x; this.y = y; }
        public Pos[] neighbours() 
        { 
            return new Pos[] { new Pos(x - 1, y), new Pos(x + 1, y), new Pos(x, y - 1), new Pos(x, y + 1) }; // left, right, up. down
        }
        public bool onPath(char[][] g) 
        { 
            return x >= 0 && x < g[0].Length && y >= 0 && y < g.Length && g[y][x] == '.';  // check barrier
        }
    }
    
    public static bool PathFinder(string maze)
    {
        string[] rows = maze.Split("\n");
        char[][] grid = new char[rows.Length][];
        for (int i = 0; i < rows.Length; i++) 
            grid[i] = rows[i].ToCharArray();
        return findExit(new Pos(0, 0), grid); // start point is (0,0)
    }

    public static bool findExit(Pos p, char[][] g)
    {
        if (p.x == g.Length - 1 && p.x == p.y) // return true if size is 1
            return true;
        if (!p.onPath(g)) 
            return false; // return false if current position is not on path
        g[p.y][p.x] = 'V'; // check visited
        Pos[] n = p.neighbours();
        return findExit(n[0], g) || findExit(n[1], g) || findExit(n[2], g) || findExit(n[3], g);
    }
}
{% endhighlight %}

## Test
<figure>
  <a href="/assets/img/posts/cshap_pathfinder/0.jpg"><img src="/assets/img/posts/cshap_pathfinder/0.jpg"></a>
	<figcaption>C# Path Finder 01</figcaption>
</figure>

# Path Finder #2: shortest path

## Task
You are at position [0, 0] in maze NxN and you can only move in one of the four cardinal directions (i.e. North, East, South, West). Return the minimal number of steps to exit position [N-1, N-1] if it is possible to reach the exit from the starting position. Otherwise, return -1.

- Empty positions are marked `.`. 
- Walls are marked `W`. 
- Start and exit positions are guaranteed to be empty in all test cases.

## Solution
{% highlight C# %}
public class Finder
{
    private static int INF = int.MaxValue;
    private static int[][] graph;

    public static int PathFinder(string maze)
    {
        InitGraph(maze);
        GoTo(0, 0, 0);
        return (graph[graph.Length - 1][graph.Length - 1] == INF) ? -1 : graph[graph.Length - 1][graph.Length - 1];
    }

    private static void InitGraph(string maze)
    {
        string[] lines = maze.Split("\n");
        graph = new int[lines.Length][];

        for (int i = 0; i < lines.Length; i++)
        {
            graph[i] = new int[lines.Length];
            for (int j = 0; j < lines.Length; j++)
            {
                graph[i][j] = (lines[i][j] == 'W') ? -1 : INF; // fill INF if maze[i][j] is '.'
            }
        }
    }

    private static void GoTo(int i, int j, int step)
    {
        if (i == -1 || i == graph.Length || j == -1 || j == graph.Length || graph[i][j] <= step)
            return;

        graph[i][j] = step;

        GoTo(i, j - 1, step + 1); // left
        GoTo(i, j + 1, step + 1); // right
        GoTo(i + 1, j, step + 1); // down
        GoTo(i - 1, j, step + 1); // up
    }
}
{% endhighlight %}

## Test
<figure>
  <a href="/assets/img/posts/cshap_pathfinder/1.jpg"><img src="/assets/img/posts/cshap_pathfinder/1.jpg"></a>
	<figcaption>C# Path Finder 02</figcaption>
</figure>

# Path Finder #3: the Alpinist

## Task
You are at start location [0, 0] in mountain area of NxN and you can only move in one of the four cardinal directions (i.e. North, East, South, West). Return minimal number of climb rounds to target location [N-1, N-1]. Number of climb rounds between adjacent locations is defined as difference of location altitudes (ascending or descending).

- Location altitude is defined as an integer number (0-9).

## Solution
{% highlight C# %}
public class Finder
{
    public static int INF = int.MaxValue;

    public static int PathFinder(string maze)
    {
        var field = maze.Split('\n').Select(s => s.Select(c => Int32.Parse(c.ToString())).ToArray()).ToArray();
        field = field.Select(r => (r.Length < field[field.Length - 1].Length) ? r.Concat(Enumerable.Repeat(field[0].Last(), (field[field.Length - 1].Length - r.Length))).ToArray() : r.ToArray()).ToArray();
        return dijkstra(field, (0, 0), (field[field.Length - 1].Length - 1, field.Length - 1));
    }

    private static int dijkstra(int[][] field, (int x, int y) startPos, (int x, int y) endPos)
    {
        Func<(int x, int y), IEnumerable<(int x, int y)>> getNig = 
            pos => new (int x, int y)[] { (pos.x - 1, pos.y), (pos.x + 1, pos.y), (pos.x, pos.y - 1), (pos.x, pos.y + 1)}
                    .Where(p => p.y >= 0 && p.y < field.Length && p.x >= 0 && p.x < field[p.y].Length);

        var dist = new Dictionary<(int x, int y), int>();

        for (int y = 0; y < field.Length; y++)
            for (int x = 0; x < field[y].Length; x++)
                dist[(x, y)] = INF;

        dist[(0, 0)] = 0;

        var Q = new SortedList<(int x, int y), int>();
        Q.Add((0, 0), 0);
        while (Q.Count > 0)
        {
            var v = Q.First();
            Q.Remove(v.Key);

            foreach (var node in getNig(v.Key))
            {
                var alt = dist[v.Key] + Math.Abs(field[v.Key.y][v.Key.x] - field[node.y][node.x]);
                if (alt < dist[node])
                {
                    Q.Remove(node);
                    dist[node] = alt;
                    Q.Add(node, alt);
                }
            }
        }
        return dist[endPos];
    }
}
{% endhighlight %}

- It can be solved Dijkstra.
- But past posted Dijkstra need `Priority Queue`.
- So you should use `SortedList` instead of.

## Test
<figure>
  <a href="/assets/img/posts/cshap_pathfinder/2.jpg"><img src="/assets/img/posts/cshap_pathfinder/2.jpg"></a>
	<figcaption>C# Path Finder 03</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
