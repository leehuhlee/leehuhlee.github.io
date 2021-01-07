---
layout: post
title: "C# Priority Queue Algorithm"
date: 2021-01-08
excerpt: "Heap Algorithm"
tags: [C#, Game, Algorithm]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part2/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part2: 자료구조와 알고리즘</a></center>


## Code
* Program.cs
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

      public int Count()
      {
        return _heap.Count;
      }
    }

    class Knight : IComparable<Knight>
    {
      public int Id { get; set; }

      public int CompareTo([AllowNull] Knight other)
      {
        if (Id == other.Id)
          return 0;
  
        return Id > other.Id ? 1 : -1;
      }
    }

    class Program
    {
      static void Main(string[] args)
      {
        PriorityQueue<Knight> q = new PriorityQueue<Knight>();
        q.Push(new Knight(){ Id = 20 });
        q.Push(new Knight() { Id = 10 });
        q.Push(new Knight() { Id = 30 });
        q.Push(new Knight() { Id = 90 });
        q.Push(new Knight() { Id = 40 });

        while(q.Count() > 0)
        {
          Console.WriteLine(q.Pop().Id);
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


## where T: IComparable
* Interface
  - Make a condition on type
  - Compare two values and return result<br>
    -1: smaller<br>
    0: bigger
{% highlight java %}
  if (_heap[now].CompareTo(_heap[next]) < 0)
{% endhighlight %}


## Result
<figure>
  <a href="/assets/img/posts/cshap_priorityqueue/0.jpg"><img src="/assets/img/posts/cshap_priorityqueue/0.jpg"></a>
	<figcaption>C# Priority Queue Algorithm</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
