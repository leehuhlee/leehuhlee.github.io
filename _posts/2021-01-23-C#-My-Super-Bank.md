---
layout: post
title: "C# My Super Bank"
date: 2021-01-23
excerpt: "Basic Bank System"
tags: [C#, Game, Algorithm]
comments: false
---

<center>Reference by <a href="https://channel9.msdn.com/Series/CSharp-101">[Microsoft Tutorial] C# 101</a></center>


## Code
* BankAcount.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;
  using System.Text;
  
  namespace MySuperBank
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
          report.AppendLine($"{item.Date.ToShortDateString()}\t{item.Amount}\t{item.Notes}");
        }
        return report.ToString();
      }
    }
  }
{% endhighlight %}

* BankAcount.cs
{% highlight java %}
  using System;
  using System.Collections.Generic;
  using System.Text;

  namespace MySuperBank
  {
    public class Transaction
    {
      public decimal Amount { get; }
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

* BankAcount.cs
{% highlight java %}
  using System;

  namespace MySuperBank
  {
    class Program
    {
      static void Main(string[] args)
      {
        BankAccount account = new BankAccount("Kendra", 10000);
        Console.WriteLine($"Account {account.Number} was created for {account.Owner} with {account.Balance}");

        account.MakeWithdrawal(120, DateTime.Now, "Hammock");
		account.MakeWithdrawal(50, DateTime.Now, "Xbox Game");

        Console.WriteLine(account.GetAccountHistory());

        // Test for a negative balance.
        try
        {
          account.MakeWithdrawal(75000, DateTime.Now, "Attempt to overdraw");
        }
        catch (InvalidOperationException e)
        {
          Console.WriteLine("Exception caught trying to overdraw");
          Console.WriteLine(e.ToString());
        }

        // Test that the initial balances must be positive.
        try
        {
          BankAccount invalidAccount = new BankAccount("invalid", -55);
        }
        catch (ArgumentOutOfRangeException e)
        {
          Console.WriteLine("Exception caught creating account with negative balance");
          Console.WriteLine(e.ToString());
        }

        account.MakeDeposit(-300, DateTime.Now, "stealing");
      }
    }
  }
{% endhighlight %}

## if-throw
* if-throw
  - The idea behind catching an exception is that when you pass data somewhere and process it, you might not always know if the result will be valid, that is when you want to catch.
  - In this case, you don't want to catch the exception. You're throwing it to alert the caller that they've made a mistake in the way they called your method.
  - Catching it yourself would prevent that from happening.
{% highlight java %}
  if (amount <= 0)
  {
    throw new ArgumentOutOfRangeException(nameof(amount), "Amount of deposit must be positive");
  }
{% endhighlight %}

## StringBuilder
* StringBuilder
  - Represents a mutable string of characters.
  - This class cannot be inherited.
{% highlight java %}
  StringBuilder report = new StringBuilder();
{% endhighlight %}
* AppendLine
  - Appends the default line terminator, or a copy of a specified string and the default line terminator, to the end of this instance.
{% highlight java %}
  report.AppendLine("Date\t\tAmmount\tNote");
{% endhighlight %}


## nameof
* nameof
  - A `nameof` expression produces the name of a variable, type, or member as the string constant
{% highlight java %}
  throw new ArgumentOutOfRangeException(nameof(amount), "Amount of deposit must be positive");
{% endhighlight %}


## Result
<figure>
  <a href="/assets/img/posts/cshap_mysuperbank/0.jpg"><img src="/assets/img/posts/cshap_mysuperbank/0.jpg"></a>
	<figcaption>C# My Super Bank</figcaption>
</figure>


[Download](https://github.com/leehuhlee/CShap){: .btn}
