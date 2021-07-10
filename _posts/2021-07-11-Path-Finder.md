---
layout: post
title: "[Unity] MMO Game Contents"
date: 2021-05-25
excerpt: "MMO Game Contents"
tags: [C#, Unity, Game, MMO, 2D, DB]
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

[Download](https://github.com/leehuhlee/CShap){: .btn}
