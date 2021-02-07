---
layout: post
title: "[MSSQL] GameDB"
date: 2021-02-08
excerpt: "Microsoft SQL"
tags: [Database, MSSQL, RDBMS]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part5/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part5: 데이터베이스</a></center>


## CREATE DATABASE
* CREATE DATABASE
  - creates a new database
{% highlight SQL %}
CREATE DATABASE GameDB;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/0.jpg"><img src="/assets/img/posts/mssql_gamedb/0.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* CREATE TABLE
  - creates a new table
  - defines column and constraints
{% highlight SQL %}
CREATE TABLE accounts(
	accountId INTEGER NOT NULL,
	accountName VARCHAR(10) NOT NULL,
	coins INTEGER DEFAULT 0,
	createdTime DATETIME
);
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_gamedb/1.jpg"><img src="/assets/img/posts/mssql_gamedb/1.jpg"></a>
  <a href="/assets/img/posts/mssql_gamedb/2.jpg"><img src="/assets/img/posts/mssql_gamedb/2.jpg"></a>
  <a href="/assets/img/posts/mssql_gamedb/3.jpg"><img src="/assets/img/posts/mssql_gamedb/3.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* DROP TABLE
  - removes the table
{% highlight SQL %}
DROP TABLE accounts;
{% endhighlight %}

* ALTER TABLE
  - changes the table
  - ADD: add new column

{% highlight SQL %}
ALTER TABLE accounts
ADD lastEnterTime DATETIME;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/4.jpg"><img src="/assets/img/posts/mssql_gamedb/4.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* DROP COLUMN
  - removes the column

{% highlight SQL %}
ALTER TABLE accounts
DROP COLUMN lastEnterTime;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/5.jpg"><img src="/assets/img/posts/mssql_gamedb/5.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* ALTER COLUMN
  - changes the column

{% highlight SQL %}
ALTER TABLE accounts
ALTER COLUMN accountName VARCHAR(20) NOT NULL;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/6.jpg"><img src="/assets/img/posts/mssql_gamedb/6.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* PRIMARY KEY
  - assigns the column to primary key

{% highlight SQL %}
ALTER TABLE accounts
ADD PRIMARY KEY (accountId);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/7.jpg"><img src="/assets/img/posts/mssql_gamedb/7.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

  - You can assign primary key in GUI

<figure class="half">
  <a href="/assets/img/posts/mssql_gamedb/8.jpg"><img src="/assets/img/posts/mssql_gamedb/8.jpg"></a>
  <a href="/assets/img/posts/mssql_gamedb/9.jpg"><img src="/assets/img/posts/mssql_gamedb/9.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

  - `PRIMARY KEY` improves the performance of database

  <figure class="half">
  <a href="/assets/img/posts/mssql_gamedb/10.jpg"><img src="/assets/img/posts/mssql_gamedb/10.jpg"></a>
  <a href="/assets/img/posts/mssql_gamedb/11.jpg"><img src="/assets/img/posts/mssql_gamedb/11.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* CONSTRAINT
  - defines the name of constraint
  - it makes easier to remove the contraint

{% highlight SQL %}
ALTER TABLE accounts
ADD CONSTRAINT PK_Account PRIMARY KEY(accountId);
{% endhighlight %}

{% highlight SQL %}
ALTER TABLE accounts
DROP CONSTRAINT PK_Account;
{% endhighlight %}

## INDEX
  - for regulation, you can use `INDEX` to index data

* Clustered
  - data is saved by order(ex. alpabet, number, etc.)
  - normally, it is primary key

* Non-Clustered
  - data is saved by index

* CREATE INDEX
  - makes new non-clustered index

{% highlight SQL %}
CREATE INDEX i1 ON accounts(accountNAme);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/12.jpg"><img src="/assets/img/posts/mssql_gamedb/12.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

  - You can make index by several values

{% highlight SQL %}
CREATE UNIQUE INDEX i3 ON accounts(accountName, coins);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/15.jpg"><img src="/assets/img/posts/mssql_gamedb/15.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>


* UNIQUE
  - defines the column has dintinct values

{% highlight SQL %}
CREATE UNIQUE INDEX i2 ON accounts(accountName);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/13.jpg"><img src="/assets/img/posts/mssql_gamedb/13.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* DROP
  - removes the index

{% highlight SQL %}
DROP INDEX accounts.i1;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/14.jpg"><img src="/assets/img/posts/mssql_gamedb/14.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

* CLUSTERED
  - makes clustered index when there is not primary key in table

{% highlight SQL %}
CREATE CLUSTERED INDEX i4 ON accounts(accountName);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/16.jpg"><img src="/assets/img/posts/mssql_gamedb/16.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

## JOIN
{% highlight SQL %}
SELECT *
FROM testA
	CROSS JOIN testB;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_gamedb/17.jpg"><img src="/assets/img/posts/mssql_gamedb/17.jpg"></a>
	<figcaption>MSSQL GameDB</figcaption>
</figure>

[Download](https://github.com/leehuhlee/Database){: .btn}
