---
layout: post
title: "C# My Super Bank"
date: 2021-01-23
excerpt: ".Net Framework"
tags: [C#, DotNet]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/CSharp-101">[Microsoft Tutorial] C# 101</a></center>


## Code
* BankAcount.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Text;
  
  namespace BankyStuffLibrary
  {
	class BankAccount
	{
	  public string Number { get; }
	  public string Owner { get; set; }
	  public decimal Balance
	  {
	    get
		{
		  decimal balance = 0;
		  foreach(Transaction item in allTransactions)
		  {
            balance += item.Amount;
          }
          return balance;
        }
      }

      private static int accountNumberSeed = 1234567890;
      private List<Transaction> allTransactions = new List<Transaction>();
        
      public BankAccount(string name, decimal initialBalance)
      {
        this.Owner = name;
        MakeDeposit(initialBalance, DateTime.Now, "Initial Balance");
        this.Number = accountNumberSeed.ToString();
        accountNumberSeed++;
      }

      public void MakeDeposit(decimal amount, DateTime date, string note)
      {
        if (amount <= 0)
        {
          throw new ArgumentOutOfRangeException(nameof(amount), "Amount of deposit must be positive");
        }
        Transaction deposit = new Transaction(amount, date, note);
        allTransactions.Add(deposit);
      }

      public void MakeWithdrawal(decimal amount, DateTime date, string note)
      {
        if (amount <= 0)
        {
          throw new ArgumentOutOfRangeException(nameof(amount), "Amount of withdrawal must be positive");
        }
        if(Balance - amount < 0)
        {
          throw new InvalidOperationException("Not sufficient funds for this withdrawal");
        }
        Transaction withdrawal = new Transaction(-amount, date, note);
        allTransactions.Add(withdrawal);
      }   

      public string GetAccountHistory()
      {
        StringBuilder report = new StringBuilder();

        // HEADER
        report.AppendLine("Date\t\tAmmount\tNote");
        foreach(Transaction item in allTransactions)
        {
          // ROWS
          report.AppendLine($"{item.Date.ToShortDateString()}\t{item.AmountForHumans}\t{item.Notes}");
        }
        return report.ToString();
      }
    }
  }
{% endhighlight %}

* Transaction.cs
{% highlight C# %}
  using System;
  using System.Collections.Generic;
  using System.Text;
  using Humanizer;

  namespace BankyStuffLibrary
  {
    public class Transaction
    {
      public decimal Amount { get; }
      public string AmountForHumans 
      { 
        get
        {
          return ((int)Amount).ToWords();
        } 
      }
      public DateTime Date { get; }
      public string Notes { get; }

      public Transaction(decimal amount, DateTime date, string note)
      {
        this.Amount = amount;
        this.Date = date;
        this.Notes = note;
      }
    }
  }
{% endhighlight %}

* Program.cs
{% highlight C# %}
  using BankyStuffLibrary;
  using System;

  namespace MySuperBank
  {
    class Program
    {
      static void Main(string[] args)
      {
        BankAccount account = new BankAccount("Kendra", 10000);
        Console.WriteLine($"Account {account.Number} was created for {account.Owner} with {account.Balance}");

        account.MakeWithdrawal(50, DateTime.Now, "Xbox Game");
        account.MakeWithdrawal(5, DateTime.Now, "Coffee");
        account.MakeWithdrawal(5, DateTime.Now, "Diet Coke");
        account.MakeWithdrawal(120, DateTime.Now, "Hammock");
        account.MakeWithdrawal(7, DateTime.Now, "Tea");
        account.MakeWithdrawal(8, DateTime.Now, "Pants");

        Console.WriteLine(account.GetAccountHistory());

        account.MakeDeposit(-300, DateTime.Now, "stealing");
      }
    }
  }
{% endhighlight %}


* BasicTests.cs
{% highlight C# %}
  using BankyStuffLibrary;
  using System;
  using Xunit;

  namespace BankingTests
  {
    public class BasicTests
    {
      [Fact]
      public void TrueIsTrue()
      {
        Assert.True(true);
      }

      [Fact]
      public void CantTakeMoreThanYourHave()
      {
        BankAccount account = new BankAccount("Kendra", 10000);

        // Test for a negative balance.
        Assert.Throws<InvalidOperationException>(
          () => account.MakeWithdrawal(75000, 
                DateTime.Now, 
                "Attempt to overdraw")
        );
      }

      [Fact]
      public void NeedMoneyToStart()
      {
        // Test that the initial balances must be positive.
        Assert.Throws<ArgumentOutOfRangeException>(
          () =>  new BankAccount("invalid", -55)
        );
      }
    }
  }
{% endhighlight %}


## if-throw
* if-throw
  - The idea behind catching an exception is that when you pass data somewhere and process it, you might not always know if the result will be valid, that is when you want to catch.
  - In this case, you don't want to catch the exception. You're throwing it to alert the caller that they've made a mistake in the way they called your method.
  - Catching it yourself would prevent that from happening.
{% highlight C# %}
  if (amount <= 0)
  {
    throw new ArgumentOutOfRangeException(nameof(amount), "Amount of deposit must be positive");
  }
{% endhighlight %}

## StringBuilder
* StringBuilder
  - Represents a mutable string of characters.
  - This class cannot be inherited.
{% highlight C# %}
  StringBuilder report = new StringBuilder();
{% endhighlight %}
* AppendLine
  - Appends the default line terminator, or a copy of a specified string and the default line terminator, to the end of this instance.
{% highlight C# %}
  report.AppendLine("Date\t\tAmmount\tNote");
{% endhighlight %}


## nameof
* nameof
  - A `nameof` expression produces the name of a variable, type, or member as the string constant
{% highlight C# %}
  throw new ArgumentOutOfRangeException(nameof(amount), "Amount of deposit must be positive");
{% endhighlight %}


## Break Point
* Condition in Break Point
  - When you use a Break Point, you can add conditions in the Break Point
  - Right click, click the [Contidion]
<figure>
  <a href="/assets/img/posts/cshap_mysuperbank/1.jpg"><img src="/assets/img/posts/cshap_mysuperbank/1.jpg"></a>
	<figcaption>C# My Super Bank</figcaption>
</figure>


## Add Project
When you need them to reference, right click [Dependency]
* Add Library
  - C# Class Library
* Add Test
  - xUnit Test
<figure>
  <a href="/assets/img/posts/cshap_mysuperbank/3.jpg"><img src="/assets/img/posts/cshap_mysuperbank/3.jpg"></a>
	<figcaption>C# My Super Bank</figcaption>
</figure>


## Pakages
* Humanizer
  - <a href="https://www.nuget.org/packages/Humanizer.Core/">Humanizer.Core</a>
  - contains the library and the neutral language (English) resources.
{% highlight C# %}
  return ((int)Amount).ToWords();
{% endhighlight %}


## Test
* [Fract]
  - It belongs to the xUnit unit testing framework.
  - It says that the method is a unit test.
* Assert
  - A collection of helper classes to test various conditions within unit tests. 
  - If the condition being tested is not met, an exception is thrown.
* Throw
  - A collection of helper classes to test various conditions within unit tests. 
  - If the condition being tested is not met, an exception is thrown.


## Publish
* Publish
  - From .Net Core, you can publish Window, IOS, Linux versions.
<figure>
  <a href="/assets/img/posts/cshap_mysuperbank/2.jpg"><img src="/assets/img/posts/cshap_mysuperbank/2.jpg"></a>
	<figcaption>C# My Super Bank</figcaption>
</figure>


## Result
<figure>
  <a href="/assets/img/posts/cshap_mysuperbank/0.jpg"><img src="/assets/img/posts/cshap_mysuperbank/0.jpg"></a>
	<figcaption>C# My Super Bank</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
