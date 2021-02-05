---
layout: post
title: "[MSSQL] Baseball"
date: 2021-02-05
excerpt: "Microsoft SQL"
tags: [Database, MSSQL, RDBMS]
comments: false
---

<center>Reference by <a href="https://www.inflearn.com/course/%EC%9C%A0%EB%8B%88%ED%8B%B0-MMORPG-%EA%B0%9C%EB%B0%9C-part5/dashboard">[C#과 유니티로 만드는 MMORPG 게임 개발 시리즈]Part5: 데이터베이스</a></center>


## Restore Database
* bak
  - Database Backup file
* SQlskills
  - <a href="https://www.sqlskills.com/sql-server-resources/sql-server-demos/">Download Baseball</a>
* Restore database
  - Right click `database` folder and click [Restore Database]
  - Add your file and click [OK] button
<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/1.jpg"><img src="/assets/img/posts/mssql_baseball/1.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/0.jpg"><img src="/assets/img/posts/mssql_baseball/0.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>


## Table
* Create new table in GUI
  - Right click `table` folder and click [new]-[table]
  - Input your data
  - Input table name, for example, `dbo.~`
<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/2.jpg"><img src="/assets/img/posts/mssql_baseball/2.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/4.jpg"><img src="/assets/img/posts/mssql_baseball/4.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* Change Design in GUI
  - Right click your table and click [design]
  - You can edit your table design in GUI
<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/3.jpg"><img src="/assets/img/posts/mssql_baseball/3.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/4.jpg"><img src="/assets/img/posts/mssql_baseball/4.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* Change Row in GUI
  - Right click your table and click [edit rows in top 200]
  - You can edit your table row in GUI
<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/6.jpg"><img src="/assets/img/posts/mssql_baseball/6.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/5.jpg"><img src="/assets/img/posts/mssql_baseball/5.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* Select rows
  - Right click your table and click [select rows in top 1000]
  - You can read your rows in top 1000
<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/7.jpg"><img src="/assets/img/posts/mssql_baseball/7.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/8.jpg"><img src="/assets/img/posts/mssql_baseball/8.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>


## SELECT FROM WHERE
* SELECT
  - define column what you want to see in table

* FROM
  - define table you want to read

* WHERE
  - define condition
  - `=` is `equal`
  - `AND` is `&&` and `OR` is `||`
  - `%` means every length word, and `_` is only one character
  - `IS NULL` means this data can be null, and `IS NOT NULL` is vice versa.

* Compile order
  - `FROM` → `WHERE` → `SELECT`
{% highlight SQL %}
SELECT *
  FROM players
 WHERE deathYear IS NULL;

SELECT nameFirst, nameLast, birthYear, birthCountry
  FROM players
 WHERE birthYear = 1974 AND birthCountry != 'USA' AND weight > 185;

 SELECT *
   FROM players
  WHERE birthCity LIKE 'New%' or  birthCity LIKE 'New Your_';
{% enghighlight %}


## ORDER BY
* ORDER BY
  - Sort the table with specified condition
* TOP
  - defines how many rows to read
* DESC and ASC
  - `DESC`: sort data by descending
  - `ASC`: sort data by ascending
* PERCENT
  - is used with `TOP`
  - defines how many rows with calculating percent to read
* OFFSET # ROWS FETCH NEXT # ROWS ONLY
  - `OFFSET` defines how many rows to read
  - `FETCH NEXT` means read rows after #th rows
{% highlight SQL %}
SELECT TOP 10 *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear DESC, birthMonth DESC, birthDay DESC;

SELECT TOP 10 PERCENT *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear DESC, birthMonth DESC, birthDay DESC;

SELECT *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear DESC, birthMonth DESC, birthDay DESC
OFFSET 100 ROWS FETCH NEXT 100 ROWS ONLY;
{% endhighlight %}


## Calculate and String
* Calculate
 - You can use arithmetic operations, for example, `+`, `-`, `/` and `*`
 - `ROUND` is to round value
 - You can also use Triangle function, for example, `COS`, `SIN` and `TAN`
{% highlight SQL %}
SELECT 2021 - birthYear As KoreanAge
FROM players
WHERE deathYear IS NULL AND birthYear IS NOT NULL AND 2021 - birthYear <= 80
ORDER BY KoreanAge;

SELECT ROUND(3.141592, 3);

SELECT COS(0);
{% endhighlight %}

* String
  - To read string that each character is 2-Byte, you should input `N` in front of the string
  - `SUBSTRING` slices the string, but start point is `1`
  - `TRIM` erases blank in the string
  - You can link several strings by `+`
{% highlight SQL %}
SELECT N'안녕하세요';

SELECT SUBSTRING('20200425', 1, 4);

SELECT TRIM('          HelloWorld');

SELECT nameFirst + ' ' + nameLast AS fullName
From players
WHERE nameFirst IS NOT NULL AND nameLast IS NOT NULL;
{% endhighlight %}

## DATETIME



[Download](https://github.com/leehuhlee/CShap){: .btn}
