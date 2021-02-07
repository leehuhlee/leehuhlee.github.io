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
  - `IS NULL` means this data can be null, and `IS NOT NULL` is vice versa.<br>
    <b>Don't use `=` when you want to `null` check, it's always null!</b>

* Compile order
  - `FROM` → `WHERE` → `SELECT`

{% highlight SQL %}
SELECT *
  FROM players
 WHERE deathYear IS NULL;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/10.jpg"><img src="/assets/img/posts/mssql_baseball/10.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT nameFirst, nameLast, birthYear, birthCountry
  FROM players
 WHERE birthYear = 1974 AND birthCountry != 'USA' AND weight > 185;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/11.jpg"><img src="/assets/img/posts/mssql_baseball/11.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
 SELECT *
   FROM players
  WHERE birthCity LIKE 'New%';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/12.jpg"><img src="/assets/img/posts/mssql_baseball/12.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
 SELECT *
   FROM players
  WHERE birthCity LIKE 'New Yor_';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/13.jpg"><img src="/assets/img/posts/mssql_baseball/13.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>


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
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/14.jpg"><img src="/assets/img/posts/mssql_baseball/14.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT TOP 10 PERCENT *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear DESC, birthMonth DESC, birthDay DESC;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/15.jpg"><img src="/assets/img/posts/mssql_baseball/15.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM players
WHERE birthYear IS NOT NULL
ORDER BY birthYear DESC, birthMonth DESC, birthDay DESC
OFFSET 100 ROWS FETCH NEXT 100 ROWS ONLY;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/16.jpg"><img src="/assets/img/posts/mssql_baseball/16.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>


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
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/17.jpg"><img src="/assets/img/posts/mssql_baseball/17.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT ROUND(3.141592, 3);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/18.jpg"><img src="/assets/img/posts/mssql_baseball/18.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT COS(0);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/19.jpg"><img src="/assets/img/posts/mssql_baseball/19.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* String
  - To read string that each character is 2-Byte, you should input `N` in front of the string
  - `SUBSTRING` slices the string, but start point is `1`
  - `TRIM` erases blank in the string
  - You can link several strings by `+`

* SELECT # AS
  - define column name by context after the `AS`

{% highlight SQL %}
SELECT N'안녕하세요';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/20.jpg"><img src="/assets/img/posts/mssql_baseball/20.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT SUBSTRING('20200425', 1, 4);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/21.jpg"><img src="/assets/img/posts/mssql_baseball/21.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT TRIM('          HelloWorld');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/22.jpg"><img src="/assets/img/posts/mssql_baseball/22.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT nameFirst + ' ' + nameLast AS fullName
From players
WHERE nameFirst IS NOT NULL AND nameLast IS NOT NULL;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/23.jpg"><img src="/assets/img/posts/mssql_baseball/23.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## DATETIME
* Create new table in GUI
  - Save table name `DateTimeTest`

* INSERT in GUI
  - Right click the table and click [Table Scripting]-[INSERT]-[New Query editor window]
<figure>
  <a href="/assets/img/posts/mssql_baseball/9.jpg"><img src="/assets/img/posts/mssql_baseball/9.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* Date Syntax
  - YYYYMMDD
  - YYYYMMDD hh:mm
  - YYYYMMDD hh:mm:ss
  - YYYYMMDD hh:mm:ss.nnn

* GETUTCDATE
  - Get time by UCT Criteria

* CAST(# AS DATETIME)
  - You can change type of string to `DATETIME`

* DATEADD
  - Adds date between DATETIME values

* DATEDIFF
  - Sub date between DATETIME values

* DATEPART
  - Extract value by type in DATETIME value
  - You can just write type what you want to extract
  
{% highlight SQL %}
SELECT GETUTCDATE();
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/24.jpg"><img src="/assets/img/posts/mssql_baseball/24.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT *
FROM DateTimeTest
WHERE time >= CAST('20100101' AS DATETIME);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/25.jpg"><img src="/assets/img/posts/mssql_baseball/25.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DATEADD(YEAR, 1, '20200426');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/26.jpg"><img src="/assets/img/posts/mssql_baseball/26.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DATEADD(DAY, 5, '20200426');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/27.jpg"><img src="/assets/img/posts/mssql_baseball/27.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DATEADD(SECOND, -121323, '20200426');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/28.jpg"><img src="/assets/img/posts/mssql_baseball/28.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DATEDIFF(DAY, '20200826', '20200503');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/29.jpg"><img src="/assets/img/posts/mssql_baseball/29.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DATEPART(DAY, '20200826');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/30.jpg"><img src="/assets/img/posts/mssql_baseball/30.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT YEAR('20200826');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/31.jpg"><img src="/assets/img/posts/mssql_baseball/31.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## CASE
* CASE WHEN ELSE END
  - smillar with switch
  - `WHEN` defines condition and `THEN` defines result
  - `ELSE` specifies result when there is not match condition with this value

{% highlight SQL %}
SELECT
	CASE birthMonth
		WHEN 1 THEN 'Winter'
		WHEN 2 THEN 'Winter'
		WHEN 3 THEN 'Spring'
		WHEN 4 THEN 'Spring'
		WHEN 5 THEN 'Spring'
		WHEN 6 THEN 'Summer'
		WHEN 7 THEN 'Summer'
		WHEN 8 THEN 'Summer'
		WHEN 9 THEN 'Autumn'
		WHEN 10 THEN 'Autumn'
		WHEN 11 THEN 'Autumn'
		WHEN 12 THEN 'Winter'
		ELSE 'NONE'
	END AS birthSeason
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/32.jpg"><img src="/assets/img/posts/mssql_baseball/32.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT
	CASE
		WHEN birthMonth <= 2 THEN 'Witer'
		WHEN birthMonth <= 5 THEN 'Spring'
		WHEN birthMonth <= 8 THEN 'Summer'
		WHEN birthMonth <= 11 THEN 'Autumn'
		ELSE 'NONE'
	END AS birthSeason
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/33.jpg"><img src="/assets/img/posts/mssql_baseball/33.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>


## Aggregation function
{% highlight SQL %}
SELECT COUNT(*)
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/34.jpg"><img src="/assets/img/posts/mssql_baseball/34.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT COUNT(birthYear)
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/35.jpg"><img src="/assets/img/posts/mssql_baseball/35.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DISTINCT birthCity
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/36.jpg"><img src="/assets/img/posts/mssql_baseball/36.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DISTINCT birthYear, birthMonth, birthDay
FROM players
ORDER BY birthYear;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/37.jpg"><img src="/assets/img/posts/mssql_baseball/37.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT COUNT(DISTINCT(birthCity))
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/38.jpg"><img src="/assets/img/posts/mssql_baseball/38.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT AVG(weight)
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/39.jpg"><img src="/assets/img/posts/mssql_baseball/39.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT SUM(weight) / COUNT(weight)
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/40.jpg"><img src="/assets/img/posts/mssql_baseball/40.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT AVG(CASE WHEN weight IS NULL THEN 0 ELSE weight END)
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/41.jpg"><img src="/assets/img/posts/mssql_baseball/41.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT MIN(weight), MAX(weight)
FROM players;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/42.jpg"><img src="/assets/img/posts/mssql_baseball/42.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## GROUP BY
* GROUP BY
  - makes group by specified column
  - You can make group by several columns

* HAVING
  - define condition in `GROUP BY`

{% highlight SQL %}
SELECT teamID, COUNT(teamID) AS playerCount, SUM(HR) AS homeRuns
FROM batting
WHERE yearID=2004
GROUP BY teamID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/43.jpg"><img src="/assets/img/posts/mssql_baseball/43.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT TOP 1 teamID, SUM(HR) AS homeRuns
FROM batting
WHERE yearID=2004
GROUP BY teamID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/44.jpg"><img src="/assets/img/posts/mssql_baseball/44.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT teamID, SUM(HR) AS homeRuns
FROM batting
WHERE yearID=2004
GROUP BY teamID
HAVING SUM(HR) >= 200
ORDER BY homeRuns DESC;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/45.jpg"><img src="/assets/img/posts/mssql_baseball/45.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT DISTINCT(teamID), yearID, SUM(HR) AS homeRuns
FROM batting
GROUP By teamID, yearID
ORDER BY homeRuns DESC;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/46.jpg"><img src="/assets/img/posts/mssql_baseball/46.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## INSERT
* INSERT
  - Adds row in table
  - in VALUE, You should put data that is not null
  - if data can be null, you don't need to fill the data

{% highlight SQL %}
INSERT INTO salaries
VALUES (2020, 'KOR', 'NL', 'Hanna', 99999);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/47.jpg"><img src="/assets/img/posts/mssql_baseball/47.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
INSERT INTO salaries(yearID, teamID, playerID, lgID, salary)
VALUES (2020, 'KOR', 'Hanna2', 'NL', 88888);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/48.jpg"><img src="/assets/img/posts/mssql_baseball/48.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
INSERT INTO salaries(yearID, teamID, playerID, lgID)
VALUES (2020, 'KOR', 'Hanna2', 'NL');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/49.jpg"><img src="/assets/img/posts/mssql_baseball/49.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/50.jpg"><img src="/assets/img/posts/mssql_baseball/50.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## DELETE
* DELETE
  - deletes specified row

{% highlight SQL %}
DELETE FROM salaries
WHERE playerID='Hanna2'
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/51.jpg"><img src="/assets/img/posts/mssql_baseball/51.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
DELETE FROM salaries
WHERE yearID >= 2020;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/53.jpg"><img src="/assets/img/posts/mssql_baseball/53.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## UPDATE
* UPDATE
  - changes specified data in `SET`
  - `SET` defines how to change the data

{% highlight SQL %}
UPDATE salaries
SET salary = salary * 2, yearID = yearID + 1
WHERE teamID='KOR';
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/52.jpg"><img src="/assets/img/posts/mssql_baseball/52.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>


## SUBQUERY
* Double SELECT statement
  - Select query from the select

{% highlight SQL %}
SELECT playerID
FROM players
WHERE playerID = (SELECT TOP 1 playerID FROM salaries ORDER BY salary DESC);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/54.jpg"><img src="/assets/img/posts/mssql_baseball/54.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT playerID
FROM players
WHERE playerID IN (SELECT TOP 20 playerID FROM salaries ORDER BY salary DESC);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/55.jpg"><img src="/assets/img/posts/mssql_baseball/55.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
SELECT(SELECT COUNT(*) FROM players) AS playerCount, (SELECT COUNT(*) FROM batting) AS battingCount;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/56.jpg"><img src="/assets/img/posts/mssql_baseball/56.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - You can make input value from SELECT statement

{% highlight SQL %}
INSERT INTO salaries
VALUES(2020, 'KOR', 'NL', 'Hanna', (SELECT MAX(salary) FROM salaries));
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/57.jpg"><img src="/assets/img/posts/mssql_baseball/57.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - You can use `SELECT` instead of `VALUES`

{% highlight SQL %}
INSERT INTO salaries
SELECT 2020, 'KOR', 'NL', 'Hanna2', (SELECT MAX(salary) FROM salaries);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/58.jpg"><img src="/assets/img/posts/mssql_baseball/58.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - IN: It substitutes the result value of the subquery to the main query and output the result after the condition comparison.(Sub query → MAIN query)

{% highlight SQL %}
SELECT playerID
FROM players
WHERE playerID IN(SELECT playerID FROM battingpost);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/61.jpg"><img src="/assets/img/posts/mssql_baseball/61.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/64.jpg"><img src="/assets/img/posts/mssql_baseball/64.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>
  
  - EXISTS: The results of the main query are substituted to the subquery to output the results after the condition comparison(MAIN query → Sub query)

{% highlight SQL %}
SELECT playerID
FROM players
WHERE EXISTS (SELECT playerID FROM battingpost WHERE players.playerID = battingpost.playerID);
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/62.jpg"><img src="/assets/img/posts/mssql_baseball/62.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/65.jpg"><img src="/assets/img/posts/mssql_baseball/65.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* Make new table with `INSERT INTO`
  - seems like copy context and pate to the table
{% highlight SQL %}
INSERT INTO salaries_temp
SELECT yearID, playerID, salary FROM salaries;
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/59.jpg"><img src="/assets/img/posts/mssql_baseball/59.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/60.jpg"><img src="/assets/img/posts/mssql_baseball/60.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

## CREATE DATABASE
* CREATE DATABASE
  - creates a new database
{% highlight SQL %}
CREATE DATABASE GameDB;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/63.jpg"><img src="/assets/img/posts/mssql_baseball/63.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
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
  <a href="/assets/img/posts/mssql_baseball/66.jpg"><img src="/assets/img/posts/mssql_baseball/66.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/67.jpg"><img src="/assets/img/posts/mssql_baseball/67.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/68.jpg"><img src="/assets/img/posts/mssql_baseball/68.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
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
  <a href="/assets/img/posts/mssql_baseball/69.jpg"><img src="/assets/img/posts/mssql_baseball/69.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - DROP COLUMN: removes the column
{% highlight SQL %}
ALTER TABLE accounts
DROP COLUMN lastEnterTime;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/70.jpg"><img src="/assets/img/posts/mssql_baseball/70.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - ALTER COLUMN: changes the column
{% highlight SQL %}
ALTER TABLE accounts
ALTER COLUMN accountName VARCHAR(20) NOT NULL;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/71.jpg"><img src="/assets/img/posts/mssql_baseball/71.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

* RIMARY KEY
  - assigns the column to primary key

{% highlight SQL %}
ALTER TABLE accounts
ADD PRIMARY KEY (accountId);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/72.jpg"><img src="/assets/img/posts/mssql_baseball/72.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - You can assign primary key in GUI

<figure>
  <a href="/assets/img/posts/mssql_baseball/73.jpg"><img src="/assets/img/posts/mssql_baseball/73.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/74.jpg"><img src="/assets/img/posts/mssql_baseball/74.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
</figure>

  - `PRIMARY KEY` improves the performance of database
  <figure>
  <a href="/assets/img/posts/mssql_baseball/75.jpg"><img src="/assets/img/posts/mssql_baseball/75.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/76.jpg"><img src="/assets/img/posts/mssql_baseball/76.jpg"></a>
	<figcaption>MSSQL Baseball</figcaption>
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




[Download](https://github.com/leehuhlee/Database){: .btn}
