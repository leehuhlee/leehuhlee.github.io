---
layout: post
title: "[MSSQL] Northwind"
date: 2021-02-10
excerpt: "Microsoft SQL"
tags: [Database, MSSQL, RDBMS]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part5/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part5: 데이터베이스</a></center>

# Data Analysis

## Download Northwind Database
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

# Multiple Index

## Copy table to new table
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

## EXEC
* EXEC sp_helpindex
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

## Index performance

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
SELECT LastName
INTO TestEmployees
FROM Employees;

CREATE INDEX Index_TestEmployees
ON TestEmployees(LastName);

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

## Page Split

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

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/18.jpg"><img src="/assets/img/posts/mssql_northwind/18.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/14.jpg"><img src="/assets/img/posts/mssql_northwind/14.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/15.jpg"><img src="/assets/img/posts/mssql_northwind/15.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

# Clustered vs Non-Clustered

## Clustered
* Clustered
  - Leaf Page = Data Page
  - data is ordered by Clustered index

## Non-Clustered
* not exist Clustered Index
  - data is saved in Heal Table
  - Heap RIP → Heap Table To exract data

{% highlight SQL%}
SELECT *
INTO TestOrderDetails2 
FROM [Order Details];

SELECT *
FROM TestOrderDetails2;

CREATE INDEX Index_OrderDetails
ON TestOrderDetails2(OrderID, productID);

EXEC sp_helpindex 'TestOrderDetails2';

SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('TestOrderDetails2');

DBCC IND('Northwind', 'TestOrderDetails2', 2);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/20.jpg"><img src="/assets/img/posts/mssql_northwind/20.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/19.jpg"><img src="/assets/img/posts/mssql_northwind/19.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* HEAP RID
  - Heap RID([Page Address(4)] [File ID(2)][Slot(2)] ROW)
  - Heap Table[{Page}  {Page}  {Page}  {Page}]

{% highlight SQL %}
DBCC PAGE('Northwind', 1, 928, 3);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/21.jpg"><img src="/assets/img/posts/mssql_northwind/21.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* exist Clustered Index
  - no Heap Table. Real data is in Leaf Table
	- has real Key value of Clustered Index
  - Previous index can be changed

{% highlight SQL %}
CREATE CLUSTERED INDEX Index_OrderDetails_Clustered
ON TestOrderDetails2(OrderID);

SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('TestOrderDetails2');

DBCC IND('Northwind', 'TestOrderDetails2', 1);
DBCC IND('Northwind', 'TestOrderDetails2', 2);
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/22.jpg"><img src="/assets/img/posts/mssql_northwind/22.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/23.jpg"><img src="/assets/img/posts/mssql_northwind/23.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/24.jpg"><img src="/assets/img/posts/mssql_northwind/24.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
DBCC PAGE('Northwind', 1, 9120, 3);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/25.jpg"><img src="/assets/img/posts/mssql_northwind/25.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/26.jpg"><img src="/assets/img/posts/mssql_northwind/26.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

# Index Scan VS Index Seek

## Index Scan vs Index Seek
* Index Access Process
  - Index Scan: Search LEAF PAGE sequentially
  - Index Seek: Search with index

## CONVERT
* CONVERT
  - change type of variable

{% highlight SQL %}
CREATE TABLE TestAccess
(
	id INT NOT NULL,
	name NCHAR(50) NOT NULL,
	dummy NCHAR(1000) NULL
);

CREATE CLUSTERED INDEX TestAccess_CI
ON TestAccess(id);

CREATE NONCLUSTERED INDEX TestAccess_NCI
ON TestAccess(name);

DECLARE @i INT;
SET @i = 1;

DECLARE @i INT;
SET @i = 1;

WHILE(@i <= 500)
BEGIN
	INSERT INTO TestAccess
	VALUES(@i, 'Name' + CONVERT(VARCHAR, @i), 'Hello World' + CONVERT(VARCHAR, @i));
	SET @i = @i + 1;
END

EXEC sp_helpindex 'TestAccess';

SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('TestAccess');
{% endhighlight%}

<figure>
  <a href="/assets/img/posts/mssql_northwind/27.jpg"><img src="/assets/img/posts/mssql_northwind/27.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## Clustered

{% highlight SQL %}
DBCC IND('Northwind', 'TestAccess', 1);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/28.jpg"><img src="/assets/img/posts/mssql_northwind/28.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/29.jpg"><img src="/assets/img/posts/mssql_northwind/29.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## Non-Clustered

{% highlight SQL %}
DBCC IND('Northwind', 'TestAccess', 2);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/30.jpg"><img src="/assets/img/posts/mssql_northwind/30.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/31.jpg"><img src="/assets/img/posts/mssql_northwind/31.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## SET STATISTICS
* TIME
  - Time for Logical read

* IO
  - Number of pages read to find actual data

{% highlight SQL %}
SET STATISTICS TIME ON;
SET STATISTICS IO ON;
{% endhighlight %}

## Scan Process

* All table
  - INDEX SCAN

{% highlight SQL %}
SELECT *
FROM TestAccess;
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/32.jpg"><img src="/assets/img/posts/mssql_northwind/32.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/33.jpg"><img src="/assets/img/posts/mssql_northwind/33.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* Non-Clustered
 - INDEX SCAN + KEY LOOKUP

$$
  \begin{align*}
    Step = N * 2 + a
  \end{align*}
$$

  - N is number of rows which you want to search
  - 2 is for root → page stesp in Non-Clustered

{% highlight SQL %}
SELECT TOP 5 *
FROM TestAccess
ORDER BY name;
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/38.jpg"><img src="/assets/img/posts/mssql_northwind/38.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/39.jpg"><img src="/assets/img/posts/mssql_northwind/39.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## Seek Process

* Clustered
  - INDEX SEEK
  - root → page
  - 2 steps

{% highlight SQL %}
SELECT *
FROM TestAccess
WHERE id = 104;
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/34.jpg"><img src="/assets/img/posts/mssql_northwind/34.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/35.jpg"><img src="/assets/img/posts/mssql_northwind/35.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* Non-Clustered
  - INDEX SEEK + KEY LOOKUP
  - root → page → clustered root → page
  - page has Key of clustered index
  - 4 steps

{% highlight SQL %}
SELECT *
FROM TestAccess
WHERE name = 'name5';
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/36.jpg"><img src="/assets/img/posts/mssql_northwind/36.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/37.jpg"><img src="/assets/img/posts/mssql_northwind/37.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

# Bookmark Lookup

## SET STATISTICS
* PROFILE
  - show the actual order in which it was executed

{% highlight SQL %}
SELECT *
INTO TestOrders
FROM Orders;

SELECT *
FROM TestOrders;

CREATE NONCLUSTERED INDEX Orders_Index01
ON TestOrders(CustomerID);

SELECT index_id, name
FROM sys.indexes
WHERE object_id = object_id('TestOrders');

DBCC IND('Northwind', 'TestOrders', 2);

SET STATISTICS TIME ON;
SET STATISTICS IO ON;
SET STATISTICS PROFILE ON;
{% endhighlight %}

## WITH INDEX
* WITH INDEX
   - force index usage
   - Database choose whether index usage or not
   - if it is more better index not usage, database can choose `SCAN`

{% highlight SQL %}
SELECT *
FROM TestOrders
WHERE CustomerID = 'QUICK';
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/40.jpg"><img src="/assets/img/posts/mssql_northwind/40.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/41.jpg"><img src="/assets/img/posts/mssql_northwind/41.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/42.jpg"><img src="/assets/img/posts/mssql_northwind/42.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM TestOrders WITH(INDEX(Orders_Index01))
WHERE CustomerID = 'QUICK';
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/43.jpg"><img src="/assets/img/posts/mssql_northwind/43.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/44.jpg"><img src="/assets/img/posts/mssql_northwind/44.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/45.jpg"><img src="/assets/img/posts/mssql_northwind/45.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## Decrease Lookup
* Covered Index
  - include all column to search in index
  - but it can putsa load on the DML(CREATE, DELETE, UPDATE)

{% highlight SQL %}
CREATE NONCLUSTERED INDEX Orders_Index02
ON TestOrders(CustomerID, ShipVia);

SELECT *
FROM TestOrders WITH(INDEX(Orders_Index02))
WHERE CustomerID = 'QUICK' AND ShipVia = 3;
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/47.jpg"><img src="/assets/img/posts/mssql_northwind/47.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/48.jpg"><img src="/assets/img/posts/mssql_northwind/48.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/46.jpg"><img src="/assets/img/posts/mssql_northwind/46.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* INCLUDE
  - give a hint to index by `INCLUDE`

{% highlight SQL %}
CREATE NONCLUSTERED INDEX Orders_Index03
ON TestOrders(CustomerID) INCLUDE (ShipVia);
{% endhighlight %}

<figure class="third">
  <a href="/assets/img/posts/mssql_northwind/49.jpg"><img src="/assets/img/posts/mssql_northwind/49.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/50.jpg"><img src="/assets/img/posts/mssql_northwind/50.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/51.jpg"><img src="/assets/img/posts/mssql_northwind/51.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* Clustered
  - Only one column
  - it can put a load if there is Non-Clustred

[Download](https://github.com/leehuhlee/Database){: .btn}
