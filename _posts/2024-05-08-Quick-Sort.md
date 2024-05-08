---
layout: post
title: "[C#] Quick Sort"
date: 2024-05-08
excerpt: "Algorithm"
tags: [Algorithm, C#]
comments: false
---

# Quick Sort
- Quick sort is a widely used sorting algorithm known for its efficiency and effectiveness.
- It follows the "divide and conquer" approach to sort elements.

# Concept

## Partitioning
- Choose a pivot element from the array.
- Usually, this can be the first, last, or middle element of the array. 
- The goal of partitioning is to rearrange the elements of the array so that all elements less than the pivot are placed before it, and all elements greater than the pivot are placed after it. 

## Recursion
- Recursively apply the above process to the sub-arrays formed by partitioning until the entire array is sorted.

## Combine
- Since each partitioning step places one element (the pivot) into its final sorted position, after all partitions are done, the array is sorted.

# Implementation

* Program.cs
{% highlight C# %}
namespace QuickSort;

class Program
{
    public static void Main(string[] args)
    {
        int[] arr = { 1, 10, 5, 8, 7, 6, 4, 3, 2, 9 };
        Console.WriteLine("Original array : ");
        Common.Print(arr);
        Console.WriteLine();

        Console.WriteLine("Choose pivot: ");
        Console.WriteLine("[1] First as pivot");
        Console.WriteLine("[2] Middle as pivot");
        Console.WriteLine("[3] Last as pivot");
        var choice = Console.ReadLine();
        Console.WriteLine();

        switch (choice)
        {
            case "1":
                FirstAsPivot.Run(arr);
                break;
            case "2":
                MiddleAsPivot.Run(arr);
                break;
            case "3":
                LastAsPivot.Run(arr);
                break;
            default:
                Console.WriteLine("Choose 1 to 3");
                return;
        }

        Console.WriteLine();
        Console.WriteLine("Sorted array : ");
        Common.Print(arr);
    }
}
{% endhighlight %}

* Common.cs
{% highlight C# %}
namespace QuickSort
{
    public class Common
    {
        public static void Swap(int[] arr, int a, int b)
        {
            var temp = arr[a]; 
            arr[a] = arr[b]; 
            arr[b] = temp;
        }

        public static void Print(int[] arr)
        {
            foreach(var item in arr)
            {
                Console.Write(item + " ");
            }
            Console.WriteLine();
        }
    }
}
{% endhighlight %}

## First element as pivot

* FirstAsPivot.cs
{% highlight C# %}
namespace QuickSort
{
    public class FirstAsPivot
    {
        public static int[] Run(int[] arr) 
        {
            QuickSort(arr, 0, arr.Length - 1);

            return arr;
        }

        private static void QuickSort(int[] arr, int start, int end)
        {
            if (start >= end) return;

            int pivot = start;
            int left = start + 1;
            int right = end;
            int temp;

            while(left <= right) 
            {
                while (left <= end && arr[left] < arr[pivot] ) left++;
                while (right > start && arr[right] > arr[pivot] ) right--;

                if (left > right) Common.Swap(arr, pivot, right);
                else Common.Swap(arr, left, right);

                Common.Print(arr);
            }

            QuickSort(arr, start, right - 1);
            QuickSort(arr, right + 1, end);
        }
    }
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/quick-sort/0.jpg"><img src="/assets/img/posts/quick-sort/0.jpg"></a>
	<figcaption>Jekyll Custom Github Blog</figcaption>
</figure>

* Round 1
- Index `pivot` is 0 and `pivot` value 1. Index `left` is 1 and `left` value is 10. Index `right` is 9 and `right` value is 9.
- Compare 1 and 10. 10 is bigger then 1. So, index `left` is 1 and `left` value is 10 for swapping.
- Compare 1 and 9. Honestly, 1 is smaller then every elements. So, we should move index `right` until 0 and result `right` value is 1 for swapping.
- It will swap 1 to 1. So, nothing ganna be changed.

* Round 2
- In first recursive, `start` is 1 and `end` is -1. So, it will return directly.

* Round 3
- In second recursive, `start` is 1 and `end` is 9. 
- Index `pivot` is 1 and `pivot` value is 10. Index `left` is 2 and `left` value is 5. Index `right` is 9 and `right` value is 9.
- Compare 10 and 5. Honestly, 10 is bigger then every elements. So, we should move index `left` until 10.
- Compare 10 and 9. 9 is smaller then 10. So, index `right` is 9 and `right` value is 9 for swapping.
- Compare index `left` and `right`. I mean, compare 10 and 9. 10 is bigger then 9. So, swap `pivot` and `right`.
- Now our array is `1, 9, 5, 8, 7, 6, 4, 3, 2, 10`.

* Round 4
- In third recursive, `start` is 1 and `end` is 8.
- After continuing quick sort, you will get `1, 2, 3, 4, 5, 6, 7, 8, 9, 10` as a result of quick sort.

## Middle element as pivot



## Last element as pivot


