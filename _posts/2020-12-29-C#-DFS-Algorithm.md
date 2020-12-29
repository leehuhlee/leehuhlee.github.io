---
layout: post
title: "C# DFS Algorithm"
date: 2020-12-29
excerpt: "DFS Algorithm"
tags: [C#, Game, Algorithm]
comments: false
---


<center>See <a href="https://github.com/leehuhlee/CShap"><b>this C# codes</b></a> in github.</center>


## Code
* Program.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;

  namespace DFSAlgorithm
  {
    class Graph
    {
      int[,] adj = new int[6, 6]
      {
        { 0, 1, 0, 1, 0, 0 },
        { 1, 0, 1, 1, 0, 0 },
        { 0, 1, 0, 0, 0, 0 },
        { 1, 1, 0, 0, 1, 0 },
        { 0, 0, 0, 1, 0, 1 },
        { 0, 0, 0, 0, 1, 0 },
      };

      List<int>[] adj2 = new List<int>[]
      {
        new List<int>(){ 1, 3 },
        new List<int>(){ 0, 2, 3 },
        new List<int>(){ 1 },
        new List<int>(){ 0, 1, 4 },
        new List<int>(){ 3, 5 },
        new List<int>(){ 4 },
      };

      public bool[] visited = new bool[6];

      // 1, visit now
      // 2. visit nodes who is adjacent with now and unvisited
      public void DFS(int now)
      {
        Console.WriteLine(now);
        visited[now] = true; // 1.

        for(int next=0; next<6; next++)
        {
          if (adj[now, next] == 0) // If next is not linked, skip
            continue;
          if (visited[next])  // If next is already visited, skip
            continue;
          DFS(next);
        }
      }

      public void DFS2(int now)
      {
        Console.WriteLine(now);
        visited[now] = true; // 1.

        foreach(int next in adj2[now])
        {
          if (visited[next])
            continue;
          DFS2(next);
        }
      }

      public void SearchAll()
      {
        visited = new bool[6];
        for (int now = 0; now < 6; now++)
          if (visited[now] == false)
            DFS(now);
      }
    } 

    class Program
    {
      static void Main(string[] args)
      {
        Graph graph = new Graph();
        Console.WriteLine("=== First DFS ===");
        graph.DFS(0);
        graph.visited = new bool[6];
        Console.WriteLine("=== Second DFS ===");
        graph.DFS2(0);
        Console.WriteLine("=== Third DFS ===");
        graph.SearchAll();
      }
    }
  }
{% endhighlight %}

## Algorithm
* DFS
  - Visit adjacent node first
* SearchAll
  - If the graph is not only one, search unlinked node
{% highlight java %}
  int[,] adj = new int[6, 6]
  {
    { 0, 1, 0, 1, 0, 0 },
    { 1, 0, 1, 1, 0, 0 },
    { 0, 1, 0, 0, 0, 0 },
    { 1, 1, 0, 0, 0, 0 },
    { 0, 0, 0, 0, 0, 1 },
    { 0, 0, 0, 0, 1, 0 },
  };

  List<int>[] adj2 = new List<int>[]
  {
    new List<int>(){ 1, 3 },
    new List<int>(){ 0, 2, 3 },
    new List<int>(){ 1 },
    new List<int>(){ 0, 1 },
    new List<int>(){ 5 },
    new List<int>(){ 4 },
  };
{% endhighlight %}


## Result
<figure class="third">
  <a href="/assets/img/posts/cshap_dfs_algorithm/0.jpg"><img src="/assets/img/posts/cshap_dfs_algorithm/0.jpg"></a>
  <a href="/assets/img/posts/cshap_dfs_algorithm/1.jpg"><img src="/assets/img/posts/cshap_dfs_algorithm/1.jpg"></a>
  <a href="/assets/img/posts/cshap_dfs_algorithm/2.jpg"><img src="/assets/img/posts/cshap_dfs_algorithm/2.jpg"></a>
	<figcaption>C# DFS Algorithm</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
