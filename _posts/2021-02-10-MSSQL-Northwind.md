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


# Index Column Order

## Create dummy data
{% highlight SQL %}
USE Northwind;

SELECT *
INTO TestOrders
FROM Orders;

DECLARE @i INT = 1;
DECLARE @emp INT;

SELECT @emp = MAX(EmployeeID) 
FROM Orders;

SELECT *
FROM TestOrders;

WHILE(@i < 1000)
BEGIN
	INSERT INTO TestOrders(CustomerID, EmployeeID, OrderDate)
	SELECT CustomerID, @emp + @i, OrderDate
	FROM Orders;
	SET @i = @i + 1;
END

SELECT COUNT(*)
FROM TestOrders;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/52.jpg"><img src="/assets/img/posts/mssql_northwind/52.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## Create Index
  - `idx_emp_ord` creates index by `EmployeeID` at first, and then by `OrderDate`
  - `idx_ord_emp` creates index by `OrderDate` at first, and then by `EmployeeID`

{% highlight SQL %}
CREATE NONCLUSTERED INDEX idx_emp_ord
ON TestOrders(EmployeeID, OrderDate);

CREATE NONCLUSTERED INDEX idx_ord_emp
ON TestOrders(OrderDate, EmployeeID);

SET STATISTICS TIME ON;
SET STATISTICS IO ON;
{% endhighlight %}

## Compare
* Case 1: no range in search
  - there is same logiacal read time
  - and both use index to search

{% highlight SQL%}
SELECT *
FROM TestOrders WITH(INDEX(idx_emp_ord))
WHERE EmployeeID = 1 AND OrderDate = CONVERT(DATETIME, '19970101');
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/53.jpg"><img src="/assets/img/posts/mssql_northwind/53.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/54.jpg"><img src="/assets/img/posts/mssql_northwind/54.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM TestOrders WITH(INDEX(idx_ord_emp))
WHERE EmployeeID = 1 AND OrderDate = CONVERT(DATETIME, '19970101');
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/55.jpg"><img src="/assets/img/posts/mssql_northwind/55.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/56.jpg"><img src="/assets/img/posts/mssql_northwind/56.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

  - first, database searches the page which have a real data matching with condition
  - and check the next row
  - if the next row is not matched with condition, then return
  - So, `idx_emp_ord` and `idx_emp_ord` searched same count of row
  - this is because their search order did not affect to search

{% highlight SQL %}
SELECT *
FROM TestOrders
ORDER BY EmployeeID, OrderDate;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/57.jpg"><img src="/assets/img/posts/mssql_northwind/57.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM TestOrders
ORDER BY OrderDate, EmployeeID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/58.jpg"><img src="/assets/img/posts/mssql_northwind/58.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* Case 2: range in search
  - both use index also
  - but both of logical read time are different

{% highlight SQL%}
-- Same with next SELECT statement ---------------
SELECT *
FROM TestOrders WITH(INDEX(idx_emp_ord))
WHERE EmployeeID = 1 AND OrderDate >= '19970101' AND OrderDate <= '19970103';
--------------------------------------------------

SELECT *
FROM TestOrders WITH(INDEX(idx_emp_ord))
WHERE EmployeeID = 1 AND OrderDate BETWEEN '19960701' AND '19970103';
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/59.jpg"><img src="/assets/img/posts/mssql_northwind/59.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/60.jpg"><img src="/assets/img/posts/mssql_northwind/60.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM TestOrders WITH(INDEX(idx_ord_emp))
WHERE EmployeeID = 1 AND OrderDate BETWEEN '19960701' AND '19970103';
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_northwind/61.jpg"><img src="/assets/img/posts/mssql_northwind/61.jpg"></a>
  <a href="/assets/img/posts/mssql_northwind/62.jpg"><img src="/assets/img/posts/mssql_northwind/62.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

  - in `idx_emp_ord`, database searches the page which match with `1` in `EmployeeID`, and then match with between `19970101` and `19970103` in `OrderDate`
  - and check next row
  - it is ordered by `EmployeeID` first, so next row must have same or bigger `EmployeeID`
  - So, `idx_emp_ord` searched only 3 rows

{% highlight SQL %}
SELECT *
FROM TestOrders
ORDER BY EmployeeID, OrderDate;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/63.jpg"><img src="/assets/img/posts/mssql_northwind/63.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

  - in `idx_ord_emp`, database searches the page wich match with between `19970101` and `19970103` in `OrderDate`, and then match with `1` in `EmployeeID`
  - and check next row
  - it is ordered by `OrderDate` first, So if the `OrderDate` is in between `19970101` and `19970103`, then database have to check `EmployeeID` in the rows
  - So `idx_ord_emp` searched more then 3 rows

{% highlight SQL %}
SELECT *
FROM TestOrders
ORDER BY OrderDate, EmployeeID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/64.jpg"><img src="/assets/img/posts/mssql_northwind/64.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

## IN-LIST
* IN()
  - if between range is small, then change the range to `IN()`

{% highlight SQL %}
SET STATISTICS PROFILE ON;

SELECT *
FROM TestOrders WITH(INDEX(idx_ord_emp))
WHERE EmployeeID = 1 AND OrderDate IN('19960101','19970102', '19970103');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/65.jpg"><img src="/assets/img/posts/mssql_northwind/65.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>


{% highlight SQL %}
SELECT *
FROM TestOrders WITH(INDEX(idx_emp_ord))
WHERE EmployeeID = 1 AND OrderDate IN('19960101','19970102', '19970103');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/66.jpg"><img src="/assets/img/posts/mssql_northwind/66.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

# Join

## Hash Join
  - Hash Join does not need to sort → The more data to Merge, The better to Hash
  - not be infected by Index
	- Hash is better than NL/Merge(depends on situation)
	- But you have to concider the cost of hash table(process time become bigger → Index)
  - Random Access X
  - smaller data is good to make Hash Table

{% highlight SQL %}
SELECT *
FROM TestOrders AS o
	INNER JOIN TestCustomers AS c
	ON o.CustomerID = c.CustomerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_northwind/67.jpg"><img src="/assets/img/posts/mssql_northwind/67.jpg"></a>
	<figcaption>MSSQL Northwind</figcaption>
</figure>

* Hash Join in C#
{% highlight SQL %}
  using System;
  using System.Collections.Generic;

  namespace HashJoin
  {
    class Player
    {
      public int playerId;
    }

    class Salary
    {
      public int playerId;
    }

    class HashTable
    {
      int _bucketCount;
      List<int>[] _buckets;

      public HashTable(int bucketCount = 100)
      {
        _bucketCount = bucketCount;
        _buckets = new List<int>[bucketCount];

        for (int i = 0; i < bucketCount; i++)
          _buckets[i] = new List<int>();
      }

      public void Add(int value)
      {
        int key = value % _bucketCount;
        _buckets[key].Add(value);
      }

      public bool Find(int value)
      {
        int key = value % _bucketCount;

        // _buckets[key].Contains(value);
        foreach(int v in _buckets[key])
        {
          if (v == value)
            return true;
        }
        return false;
      }
    }

    class Program
    {
      static void Main(string[] args)
      {
        Random rand = new Random();

        List<Player> players = new List<Player>();
        for (int i = 0; i < 1000; i++)
        {
          if (rand.Next(0, 2) == 0)
            continue;

          players.Add(new Player() { playerId = i });
        }

        List<Salary> salaries = new List<Salary>();
        for (int i = 0; i < 1000; i++)
        {
          if (rand.Next(0, 2) == 0)
            continue;

          salaries.Add(new Salary() { playerId = i });
        }

        // TEMP HashTable
        /*Dictionary<int, Salary> hash = new Dictionary<int, Salary>();       
        foreach (Salary s in salaries)
          hash.Add(s.playerId, s);

        List<int> result = new List<int>();
        foreach(Player p in players)
        {
          if (hash.ContainsKey(p.playerId))
            result.Add(p.playerId);
        }*/

        HashTable hash = new HashTable();
        foreach (Salary s in salaries)
          hash.Add(s.playerId);

        List<int> result = new List<int>();
        foreach(Player p in players)
        {
          if (hash.Find(p.playerId))
            result.Add(p.playerId);
        }
      }
    }
  }
{% endhighlight %}


[Download](https://github.com/leehuhlee/Database){: .btn}
