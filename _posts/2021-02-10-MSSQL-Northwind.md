---
layout: post
title: "[MSSQL] Northwind"
date: 2021-02-10
excerpt: "Microsoft SQL"
tags: [Database, MSSQL, RDBMS]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part5/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part5: 데이터베이스</a></center>


## DOWNLOAD NORTHWIND DATABASE
* GITHUB
  - DOWNLOAD <a href="https://github.com/microsoft/sql-server-samples/blob/master/samples/databases/northwind-pubs/instnwnd.sql">Northwind</a>

<figure>
  <a href="/assets/img/posts/mssql_northwind/0.jpg"><img src="/assets/img/posts/mssql_northwind/0.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## EXEC
* EXEC sp_helpdb
  - show information of databse

{% highlight sql%}
EXEC sp_helpdb 'Northwind';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/1.jpg"><img src="/assets/img/posts/mssql_northwind/1.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* EXE sp_helpindex
  - show information of index

{% highlight SQL %}
CREATE INDEX Index_TestOrderDetails
ON TestOrderDetails(OrderID, ProductID);

EXEC sp_helpindex 'TestOrderDetails';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/9.jpg"><img src="/assets/img/posts/mssql_northwind/9.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## INDEX
* FILLFACTOR
  - use only 1% of leaf page space

* PAD_INDEX
  - apply to FILLFACTOR middle page

{% highlight sql%}
CREATE INDEX Test_Inde ON Test(LastName)
WITH (FILLFACTOR = 1, PAD_INDEX = ON)
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/2.jpg"><img src="/assets/img/posts/mssql_northwind/2.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* sys.indexes
  - help to search index number

{% highlight SQL %}
SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('Test');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/3.jpg"><img src="/assets/img/posts/mssql_northwind/3.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## DBCC
* DBCC 
  - show database data that match with the condition

* IND
  - show index data

{% highlight SQL %}
DBCC IND('Northwind', 'Test', 2);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/4.jpg"><img src="/assets/img/posts/mssql_northwind/4.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/7.jpg"><img src="/assets/img/posts/mssql_northwind/7.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* PAGE
  - ref: [Database], [File Number], [Page Number], [Print Option]

* HEAD_RID 
  - ref: [Page Address](4byte), [File ID](2byte), [Slot Number](2byte)

{% highlight SQL %}
DBCC PAGE('Northwind', 1, 840, 3);
DBCC PAGE('Northwind', 1, 848, 3);
DBCC PAGE('Northwind', 1, 849, 3);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/5.jpg"><img src="/assets/img/posts/mssql_northwind/5.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
DBCC PAGE('Northwind', 1, 872, 3);
DBCC PAGE('Northwind', 1, 848, 3);
DBCC PAGE('Northwind', 1, 849, 3);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/6.jpg"><img src="/assets/img/posts/mssql_northwind/6.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* Random Access
  - Access one page by one page to read

* Bookmark Lookup
  - search row by RID

## COPY TABLE TO NEW TABLE
* SELECT INTO
  - copy the table and paste in a new table

{% highlight SQL %}
SELECT *
INTO TestOrderDetails
FROM [Order Details];
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/8.jpg"><img src="/assets/img/posts/mssql_northwind/8.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## INDEX PERFORMANCE

* Multiple Index
  - when you use multiple index(A, B), the index order is A and B is next level of A
  - So, if you want to use index, you have to start A
  - if you start B, then the process cannot use index

* SEEK
  - process speed is good
  - use index when searching

{% highlight SQL %}
SELECT *
FROM TestOrderDetails
WHERE OrderID = 10248 AND ProductID = 11;

SELECT *
FROM TestOrderDetails
WHERE ProductID = 11 AND OrderID = 10248;

SELECT *
FROM TestOrderDetails
WHERE OrderID = 10248;
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/10.jpg"><img src="/assets/img/posts/mssql_northwind/10.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/11.jpg"><img src="/assets/img/posts/mssql_northwind/11.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/12.jpg"><img src="/assets/img/posts/mssql_northwind/12.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM TestEmployees
WHERE LastName LIKE 'Bu%';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/17.jpg"><img src="/assets/img/posts/mssql_northwind/17.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* SCAN
  - process speed is bad
  - do not use index when searching

{% highlight SQL %}
SELECT *
FROM TestOrderDetails
WHERE ProductID = 11;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/13.jpg"><img src="/assets/img/posts/mssql_northwind/13.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

  - when you use data processing, all of data will be scanned even it is index

{% highlight SQL %}
SELECT *
FROM TestEmployees
WHERE SUBSTRING(LastName, 1, 2) = 'Bu';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/16.jpg"><img src="/assets/img/posts/mssql_northwind/16.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## PAGE SPLIT

* Page Split
  - if page is full, make a new page and insert in specific position

{% highlight SQL%}
DECLARE @i INT = 0;
WHILE @i < 50
BEGIN
	INSERT INTO TestOrderDetails
	VALUES(10248, 100 + @i, 10, 1, 0);
	SET @i = @i +1;
END
{% endhighlight%}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/14.jpg"><img src="/assets/img/posts/mssql_northwind/14.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/15.jpg"><img src="/assets/img/posts/mssql_northwind/15.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>



[Download](https://github.com/leehuhlee/Database){: .btn}