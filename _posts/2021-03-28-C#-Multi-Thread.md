---
layout: post
title: "[C#] Multi Thread"
date: 2021-03-28
excerpt: "Thread, Compiler Optimization, Cash, Memory Barrier, Lock, Thread Local Storage"
tags: [C#, Server]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-mmorpg-%EA%B0%9C%EB%B0%9C-part4/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part4: 게임 서버</a></center>

# Project

## Create Projects
* Server solution
  - Create console app and name solution to `Server`
  - Create project in `Server` solution named `DummyClient` and `ServerCore`
  - set `ServerCore` project to starting project

<figure class="half">
  <a href="/assets/img/posts/cshap_multi_thread/0.jpg"><img src="/assets/img/posts/cshap_multi_thread/0.jpg"></a>
  <a href="/assets/img/posts/cshap_multi_thread/1.jpg"><img src="/assets/img/posts/cshap_multi_thread/1.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

# Thread

## Create Thread

* ServerCore\Program.cs
  - you have to make main thread function
  - connect main thread function
  - `Start` lets main thread function start 

{% highlight C# %}
using System;
using System.Threading;
using System.Threading.Tasks;

namespace ServerCore
{
    class Program
    {
        static void MainThread()
        {
            Console.WriteLine("Hello Thread!");
        }

        static void Main(string[] args)
        {
            Thread t = new Thread(MainThread);
            t.Start();

            Console.WriteLine("Hello World!");
        }
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/2.jpg"><img src="/assets/img/posts/cshap_multi_thread/2.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## Foreground Thread
  - default is foreground thread in C#
  - if main thread has infinity funtion, then it will not finish forever

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    while(true)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    Thread t = new Thread(MainThread);
    t.Start();

    Console.WriteLine("Hello World!");
}
{% endhighlight %}

## Background Thread
  - you can change main thread to background thread by `IsBackgound` method
  - even there is infinity function in main thread, this thread will be finished because it is background thread

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    while(true)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    Thread t = new Thread(MainThread);
    t.IsBackground = true;
    t.Start();

    Console.WriteLine("Hello World!");
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/4.jpg"><img src="/assets/img/posts/cshap_multi_thread/4.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## Waiting Thread
  - main can wait our main thread by `Join` Method
  - you can define Thread name by `Name` Method

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    while(true)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    Thread t = new Thread(MainThread);
    t.Name = "Test Thread";
    t.IsBackground = true;
    t.Start();

    Console.WriteLine("Waiting for Thread!");
    t.Join();

    Console.WriteLine("Hello World!");
}
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/cshap_multi_thread/5.jpg"><img src="/assets/img/posts/cshap_multi_thread/5.jpg"></a>
  <a href="/assets/img/posts/cshap_multi_thread/6.jpg"><img src="/assets/img/posts/cshap_multi_thread/6.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## ThreadPool
  - Thread is already made in C#
  - So when we need this thread, just use `ThreadPool`
  - after their work, they are not removed, just changed to deactivate

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    for(int i=0; i<5; i++)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    ThreadPool.QueueUserWorkItem(MainThread);
    while (true){ }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/7.jpg"><img src="/assets/img/posts/cshap_multi_thread/7.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## Set number of Threads
  - you can set number of threads by `SetMinThreads` and `SetMaxThreads`

### Test: existing waiting thread
  - if there is waiting thread, then this thread will excute

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    for(int i=0; i<5; i++)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    ThreadPool.SetMinThreads(1, 1);
    ThreadPool.SetMaxThreads(5, 5);

    for (int i = 0; i < 5; i++)
        ThreadPool.QueueUserWorkItem((obj) => { while (true) { } });

    ThreadPool.QueueUserWorkItem(MainThread);
    while (true){ }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/3.jpg"><img src="/assets/img/posts/cshap_multi_thread/3.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

### Test: non existing waiting thread
  - but if there is no waiting thread because of infinity function in thread, then this project cannot be finished

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    for(int i=0; i<5; i++)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    ThreadPool.SetMinThreads(1, 1);
    ThreadPool.SetMaxThreads(5, 5);

    for (int i = 0; i < 5; i++)
        ThreadPool.QueueUserWorkItem((obj) => { while (true) { } });

    ThreadPool.QueueUserWorkItem(MainThread);
    while (true){ }
}
{% endhighlight %}

## Task
  - Task is action
  - Task is work of Thread
  - you can define this thread is infinity by `LongRunning`

* ServerCore\Program.cs
{% highlight C# %}
static void MainThread()
{
    for(int i=0; i<5; i++)
        Console.WriteLine("Hello Thread!");
}

static void Main(string[] args)
{
    ThreadPool.SetMinThreads(1, 1);
    ThreadPool.SetMaxThreads(5, 5);

    for (int i = 0; i < 5; i++)
    {
        Task t = new Task(() => { while (true) { } }, TaskCreationOptions.LongRunning);
        t.Start();
    }

    ThreadPool.QueueUserWorkItem(MainThread);

    while (true){ }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/3.jpg"><img src="/assets/img/posts/cshap_multi_thread/3.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

# Compiler Optimization
  - you can make stop duration by `Sleep`
  - `Wait` is same function with `Join`, but `Wait` is for Task and `Join` is for Thread
  - you can wait main Task by `Wait`

* ServerCore\Program.cs
{% highlight C# %}
static bool _stop = false;

static void ThreadMain()
{
    Console.WriteLine("Start Thread!");

    while(_stop == false)
    {
        // wait stop signal
    }

    Console.WriteLine("Stop Thread!");
}

static void Main(string[] args)
{
    Task t = new Task(ThreadMain);
    t.Start();

    Thread.Sleep(1000);

    _stop = true;

    Console.WriteLine("Stop Call");
    Console.WriteLine("Waiting End");

    t.Wait();

    Console.WriteLine("Success End");
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/9.jpg"><img src="/assets/img/posts/cshap_multi_thread/9.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## Release Mode
  - release mode optimize our code

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/11.jpg"><img src="/assets/img/posts/cshap_multi_thread/11.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

  - So `while` method is optimized by Release mode
  - and this makes problem(like infinity program)

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/10.jpg"><img src="/assets/img/posts/cshap_multi_thread/10.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## Disassembler
  - you can check your C# code to assembly language

* Disassember
  - make stop point in while method
  - excute program by debugging mode
  - click [Debug]-[Window]-[Disassembler]

<figure class="third">
  <a href="/assets/img/posts/cshap_multi_thread/12.jpg"><img src="/assets/img/posts/cshap_multi_thread/12.jpg"></a>
  <a href="/assets/img/posts/cshap_multi_thread/15.jpg"><img src="/assets/img/posts/cshap_multi_thread/15.jpg"></a>
  <a href="/assets/img/posts/cshap_multi_thread/13.jpg"><img src="/assets/img/posts/cshap_multi_thread/13.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

* Result
  - by optimization, while method became infinity method
  
## Volatile
  - you can avoid optimization by `volatile`

* ServerCore\Program.cs

{% highlight C# %}
volatile static bool _stop = false;
...
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/14.jpg"><img src="/assets/img/posts/cshap_multi_thread/14.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

# Cache

* Temporal Locality
  - recently used memory must have be used again

* Spacial Locality
  - close memory with recently used memory must have be used

### Test: Spacial Locality

* ServerCore\Program.cs
{% highlight C# %}
static void Main(string[] args)
{
    int[,] arr = new int [10000, 10000];

    {
        long now = DateTime.Now.Ticks;
        for (int y = 0; y < 10000; y++)
            for (int x = 0; x < 10000; x++)
                arr[y, x] = 1;
        long end = DateTime.Now.Ticks;
        Console.WriteLine($"(y, x) order spent time {end-now}");
    }

    {
        long now = DateTime.Now.Ticks;
        for (int y = 0; y < 10000; y++)
            for (int x = 0; x < 10000; x++)
                arr[x, y] = 1;
        long end = DateTime.Now.Ticks;
        Console.WriteLine($"(x, y) order spent time {end-now}");
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/16.jpg"><img src="/assets/img/posts/cshap_multi_thread/16.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

# Memory Barrier

## Hardware Optimization
  - if there is no related with each code, ordering of code can be changed

### Test

* ServerCore\Program.cs
{% highlight C# %}
class Program
{
    static int x = 0;
    static int y = 0;
    static int r1 = 0;
    static int r2 = 0;

    static void Thread_1()
    {
        y = 1; //store y
        r1 = x; // Load x
    }

    static void Thread_2()
    {
        x = 1; // Storex
        r2 = y; // Load y
    }

    static void Main(string[] args)
    {
        int count = 0;
        while (true)
        {
            count++;
            x = y = r1 = r2 = 0;

            Task t1 = new Task(Thread_1);
            Task t2 = new Task(Thread_2);
            t1.Start();
            t2.Start();

            Task.WaitAll(t1, t2);

            if (r1 == 0 && r2 == 0)
                break;
        }

        Console.WriteLine($"{count} times executed!");
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/17.jpg"><img src="/assets/img/posts/cshap_multi_thread/17.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

  - this mean, `r1=x` is ordered first then `y=1` and `r2=y` is ordered first then `x=1`

## Memory Barrier
  - To avoid Hardware optimization

* Memory Barrier 
  - avoid relocating code
  - Visibility

* Full memory Barrier(ASM MFENCE, C# Thread.MemoryBarrier)
  - ban Store and Load.

* Store Memory Barrier (ASM SFENCE)
  - ban only Store
    
* Load Memory Barrier(ASM LFENCE) 
  - ban only Load

## Memory Barrier timing

* Store
  - after store

* Load
  - before load

### Test

* ServerCore\Program.cs
{% highlight C# %}
class Program
{
    static int x = 0;
    static int y = 0;
    static int r1 = 0;
    static int r2 = 0;

    static void Thread_1()
    {
        y = 1; //store y
        Thread.MemoryBarrier();
        r1 = x; // Load x
    }

    static void Thread_2()
    {
        x = 1; // Storex
        Thread.MemoryBarrier();
        r2 = y; // Load y
    }

    static void Main(string[] args)
    {
        int count = 0;
        while (true)
        {
            count++;
            x = y = r1 = r2 = 0;

            Task t1 = new Task(Thread_1);
            Task t2 = new Task(Thread_2);
            t1.Start();
            t2.Start();

            Task.WaitAll(t1, t2);

            if (r1 == 0 && r2 == 0)
                break;
        }

        Console.WriteLine($"{count} times executed!");
    }
}
{% endhighlight %}

# Interlocked

## Race Condition
  - Tasks excute withour order

### Test

* ServerCore\Program.cs
{% highlight C# %}
class Program
{
    static int number = 0;

    static void Thread_1()
    {
        for (int i = 0; i < 100000; i++)
            number++;
    }

    static void Thread_2()
    {
        for (int i = 0; i < 100000; i++)
            number--;
    }

    static void Main(string[] args)
    {
        Task t1 = new Task(Thread_1);
        Task t2 = new Task(Thread_2);
        t1.Start();
        t2.Start();

        Task.WaitAll(t1, t2);

        Console.WriteLine(number);
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/18.jpg"><img src="/assets/img/posts/cshap_multi_thread/18.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

  - this happened because `number++` and `number--` is not atomic

## Interlocked
  - make method to atomic

### Test

* ServerCore\Program.cs
{% highlight C# %}
class Program
{
    ...

    static void Thread_1()
    {
        for (int i = 0; i < 100000; i++)
            Interlocked.Increment(ref number);
    }

    static void Thread_2()
    {
        for (int i = 0; i < 100000; i++)
            Interlocked.Decrement(ref number);
    }

    ...
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/19.jpg"><img src="/assets/img/posts/cshap_multi_thread/19.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

  - result is changed because `Interlocked.Increment` and `Interlocked.Decrement` is atomic

# Lock

## Mutual Exclusive

* Monitor
  - if code is too long to make interlocked, we can use `Monitor`
  - `Enter` means lock the task and `Exit` means solve the task
  - after `Exit`, other tasks are running

### Test

* ServerCore\Program.cs
{% highlight C# %}
class Program
{
    static int number = 0;
    static object _obj = new object();

    static void Thread_1()
    {
        for (int i = 0; i < 100000; i++)
        {
            Monitor.Enter(_obj); // Lock
            number++;
            Monitor.Exit(_obj); // Solve
        }
    }

    static void Thread_2()
    {
        for (int i = 0; i < 100000; i++)
        {
            Monitor.Enter(_obj);
            number--;
            Monitor.Exit(_obj);
        }
    }

    static void Main(string[] args)
    {
        Task t1 = new Task(Thread_1);
        Task t2 = new Task(Thread_2);
        t1.Start();
        t2.Start();

        Task.WaitAll(t1, t2);

        Console.WriteLine(number);
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/20.jpg"><img src="/assets/img/posts/cshap_multi_thread/20.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

## Lock

* Lock
  - `Lock` is same function with try-catch-finally
  - object is used like a Lock

### Test

* ServerCore\Program.cs
{% highlight C# %}
class Program
{
    static int number = 0;
    static object _obj = new object();

    static void Thread_1()
    {
        for (int i = 0; i < 100000; i++)
        {
            lock (_obj)
            {
                number++;
            }
        }
    }

    static void Thread_2()
    {
        for (int i = 0; i < 100000; i++)
        {
                lock (_obj)
                {
                    number--;
                }
            }
        }
    ...
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/cshap_multi_thread/21.jpg"><img src="/assets/img/posts/cshap_multi_thread/21.jpg"></a>
	<figcaption>C# Multi Thread</figcaption>
</figure>

[Download](https://github.com/leehuhlee/CShap){: .btn}
