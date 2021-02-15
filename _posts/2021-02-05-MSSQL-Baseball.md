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

* IN
  - substitutes the result value of the subquery to the main query and output the result after the condition comparison.(Sub query → MAIN query)

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

* EXISTS
  - The results of the main query are substituted to the subquery to output the results after the condition comparison(MAIN query → Sub query)

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

## UNION
* UNION
  - merges two select
  - Duplicate values are shown only once
  - result columns are always same in `SELECT` statements
{% highlight SQL %}
SELECT playerId
FROM salaries
GROUP BY playerId
HAVING AVG(salary) >= 3000000
UNION
SELECT playerID
FROM players
WHERE birthMonth = 12;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/66.jpg"><img src="/assets/img/posts/mssql_baseball/66.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* UNION ALL
  - selects dulicate values

{% highlight SQL %}
SELECT playerId
FROM salaries
GROUP BY playerId
HAVING AVG(salary) >= 3000000
UNION ALL
SELECT playerId
FROM players
WHERE birthMonth = 12
ORDER BY playerId;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/63.jpg"><img src="/assets/img/posts/mssql_baseball/63.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* INTERSECT
  - shows intersect results of two `SELECT` statements

{% highlight SQL %}
SELECT playerId
FROM salaries
GROUP BY playerId
HAVING AVG(salary) >= 3000000
INTERSECT
SELECT playerId
FROM players
WHERE birthMonth = 12
ORDER BY playerId;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/68.jpg"><img src="/assets/img/posts/mssql_baseball/68.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* EXCEPT
  - shows the result of subtracting the second `SELECT` statement from the first `SELECT` statement
  
{% highlight SQL %}
SELECT playerId
FROM salaries
GROUP BY playerId
HAVING AVG(salary)>=3000000
EXCEPT
SELECT playerId
FROM players
WHERE birthMonth=12
ORDER BY playerId;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/69.jpg"><img src="/assets/img/posts/mssql_baseball/69.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## JOIN

* `ON`
  - specifies the condition of `JOIN`

* INNER JOIN
  - Combine two tables horizontally

{% highlight SQL %}
SELECT p.playerID, s.salary
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/67.jpg"><img src="/assets/img/posts/mssql_baseball/67.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* OUTER JOIN
  - Combine data even if it exists on either side
  - LEFT JOIN
  - RIGHT JOIN

* LEFT JOIN
  - If there is information on the left, show it no matter what
  - If there is no information on the right, the right information is NULL

{% highlight SQL %}
SELECT p.playerID, s.salary
FROM players AS p
	LEFT JOIN salaries AS s
	ON p.playerID = s.playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/70.jpg"><img src="/assets/img/posts/mssql_baseball/70.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* RIGHT JOIN
  - If there is information on the right, show it no matter what
  - If there is no information on the left, the left information is NULL

{% highlight SQL %}
SELECT p.playerID, s.salary
FROM players AS p
	RIGHT JOIN salaries AS s
	ON p.playerID = s.playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/71.jpg"><img src="/assets/img/posts/mssql_baseball/71.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## DECLARE
* DECLARE
  - declares variable

{% highlight SQL%}
DECLARE @i AS INT = 10;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/72.jpg"><img src="/assets/img/posts/mssql_baseball/72.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* DECLARE TABLE
  - declares table

{% highlight SQL%}
DECLARE @test TABLE
(
	name VARCHAR(50) NOT NULL,
	salary INT NOT NULL
);

INSERT INTO @test
SELECT p.nameFirst + ' ' + p.nameLast, s.salary
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/72.jpg"><img src="/assets/img/posts/mssql_baseball/72.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* SET
  - input value in variable

{% highlight SQL %}
DECLARE @j AS INT;
SET @j = 10;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/73.jpg"><img src="/assets/img/posts/mssql_baseball/73.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

  - You can set value from `SELECT`

{% highlight SQL %}
DECLARE @firstName AS NVARCHAR(15);

SET @firstName=(SELECT TOP 1 nameFirst
				        FROM players AS p
					        INNER JOIN salaries AS s
					        ON p.playerID = s.playerID
				        ORDER BY s.salary DESC);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/74.jpg"><img src="/assets/img/posts/mssql_baseball/74.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

{% highlight SQL %}
DECLARE @firstName AS NVARCHAR(15);
DECLARE @lastName AS NVARCHAR(15);

SELECT TOP 1 @firstName = p.nameFirst, @lastName = p.nameLast
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID
ORDER BY s.salary DESC;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/75.jpg"><img src="/assets/img/posts/mssql_baseball/75.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## GO
* GO
  - Set Valid Range for Variables
  - A set of commands that are analyzed and executed in a single batch

<figure class="half">
  <a href="/assets/img/posts/mssql_baseball/76.jpg"><img src="/assets/img/posts/mssql_baseball/76.jpg"></a>
  <a href="/assets/img/posts/mssql_baseball/77.jpg"><img src="/assets/img/posts/mssql_baseball/77.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## IF-ELSE
* IF-ELSE
  - same with if-else statement in C#

* PRINT
  - prints content on console

{% highlight SQL %}
IF @i = 10
	PRINT('BINGO');
ELSE
	PRINT('NO');
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/78.jpg"><img src="/assets/img/posts/mssql_baseball/78.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* BEGIN-END
  - You can several process in statement

{% highlight SQL %}
IF @i=10
BEGIN
	PRINT('BINGO');
	PRINT('BINGO');
END
ELSE
BEGIN
	PRINT('NO');
END
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/79.jpg"><img src="/assets/img/posts/mssql_baseball/79.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## WHILE
* WHILE
  - same with while statement in C#

* BREAK
  - same with break statement in C#

* CONTINUE
  - same with continue statement in C#

{% highlight SQL %}
DECLARE @i AS INT = 0;
WHILE @i <=10
BEGIN
	SET @i = @i + 1;
	IF @i = 9 BREAK;
	IF @i = 6 CONTINUE;
	PRINT @i
END
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/80.jpg"><img src="/assets/img/posts/mssql_baseball/80.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## WINDOW FUNCTION
* WINDOW FUNCTION
  - always use with `OVER`

* ROW_NUMBER
  - shows number of row

* RANK
  - shows the ranking

* DENSE_RANK
  - shows rankings including ties

* NTITLE
  - show the percentage
  
{% highlight SQL %}
SELECT *,
	ROW_NUMBER() OVER (ORDER BY salary DESC),
	RANK() OVER (ORDER BY salary DESC),
	DENSE_RANK() OVER (ORDER BY salary DESC),
	NTILE(100) OVER (ORDER BY salary DESC)
FROM salaries;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/81.jpg"><img src="/assets/img/posts/mssql_baseball/81.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* PARTITION
  - divide by reference value and process

{% highlight SQL %}
SELECT *,
	RANK() OVER(PARTITION BY playerID ORDER BY salary DESC)
FROM salaries
ORDER BY playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/82.jpg"><img src="/assets/img/posts/mssql_baseball/82.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* LAG
  - show previous value

* LEAD
  - show next value

{% highlight SQL %}
SELECT *,
	RANK() OVER(PARTITION BY playerID ORDER BY salary DESC),
	LAG(salary) OVER (PARTITION BY playerID ORDER BY salary DESC) AS prevSalary,
	LEAD(salary) OVER (PARTITION BY playerID ORDER BY salary DESC) AS nextSAlary
FROM salaries
ORDER BY playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/83.jpg"><img src="/assets/img/posts/mssql_baseball/83.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* FIRST_VALUE
  - show the biggest value between previous values and current value

* LAST_VALUE
  - show the smalliest value between previous values and current value

{% highlight SQL%}
SELECT *,
	RANK() OVER (PARTITION BY playerID ORDER BY salary DESC),
	FIRST_VALUE(salary) OVER (PARTITION BY playerID ORDER BY salary DESC) AS best,
	LAST_VALUE(salary) OVER (PARTITION BY playerID ORDER BY salary DESC) AS worst
FROM salaries
ORDER BY playerID;
{% endhighlight%}

<figure>
  <a href="/assets/img/posts/mssql_baseball/84.jpg"><img src="/assets/img/posts/mssql_baseball/84.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* ROWS BETWEEN AND
  - specifies the range of rows to compare
  - `UNBOUNDED PRECEDING`: from all previous row in partition
  - `CURRENT ROW`: from/to current row
  - `UNBOUNDED FOLLOWING`: to all previous row in partition

{% highlight SQL %}
SELECT *,
	RANK() OVER (PARTITION BY playerID ORDER BY salary DESC),
	FIRST_VALUE(salary) OVER (PARTITION BY playerID 
								ORDER BY salary DESC
								ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
								) AS best,
	LAST_VALUE(salary) OVER (PARTITION BY playerID 
								ORDER BY salary DESC
								ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
								) AS worst
FROM salaries
ORDER BY playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/85.jpg"><img src="/assets/img/posts/mssql_baseball/85.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>


# JOIN

## Merge
* Sort Merge
  - Merge Join has condition
  - outer has to be unique
  - One-to-Many(PK, unique)
  - Random Access X → Clustered Scan and sorting

{% highlight SQL %}
SELECT *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/86.jpg"><img src="/assets/img/posts/mssql_baseball/86.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* Merge Join in C#

{% highlight SQL %}
  using System;
  using System.Collections.Generic;
  using System.Diagnostics.CodeAnalysis;

  namespace MergeJoin
  {
    class Player : IComparable<Player>
    {
      public int playerId;

      public int CompareTo(Player other)
      {
        if (playerId == other.playerId)
          return 0;
        return (playerId > other.playerId) ? 1 : -1;
      }
    }

    class Salary : IComparable<Salary>
    {
      public int playerId;

      public int CompareTo(Salary other)
      {
        if (playerId == other.playerId)
          return 0;
        return (playerId > other.playerId) ? 1 : -1;
      }
    }

    class Program
    {
      static void Main(string[] args)
      {
        List<Player> players = new List<Player>();
        players.Add(new Player() { playerId = 0 });
        players.Add(new Player() { playerId = 9 });
        players.Add(new Player() { playerId = 1 });
        players.Add(new Player() { playerId = 3 });
        players.Add(new Player() { playerId = 4 });

        List<Salary> salaries = new List<Salary>();
        salaries.Add(new Salary() { playerId = 0 });
        salaries.Add(new Salary() { playerId = 5 });
        salaries.Add(new Salary() { playerId = 0 });
        salaries.Add(new Salary() { playerId = 2 });
        salaries.Add(new Salary() { playerId = 9 });

        // Step 1) Sort (if already sorted, SKIP)
        // O(N * Log(N))
        players.Sort();
        salaries.Sort();

        // One-To-Many(players is unique)
        // Step 2) Merge
        // outer [0,1,3,4,9]
        // inner [0,0,2,5,9]

        int p = 0;
        int s = 0;

        List<int> result = new List<int>();

        // O(N+M)
        while (p<players.Count && s<salaries.Count)
        {
          if(players[p].playerId == salaries[s].playerId)
          {
            result.Add(players[p].playerId);
            s++;
          }
          else if(players[p].playerId < salaries[s].playerId)
          {
            p++;
          }
          else
          {
            s++;
          }
        }

        // Many-To-Many(Player is not unique)
        // outer [0,0,0,0,0] -> N
        // inner [0,0,0,0,0] -> N
        // O(N+M)
      }
    }
  }
{% endhighlight %}

* Case: Outer is clustered
  - is the best way to Merge Join

{% highlight SQL%}
SELECT *
FROM schools AS s
	INNER JOIN schoolsplayers AS p
	ON s.schoolID = p.schoolID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/91.jpg"><img src="/assets/img/posts/mssql_baseball/91.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## Nested Loop
* Nested Loop
  - access outer table which is accessed first, and then access randomly in inner table
  - if there is not index in inner table, time complexity O(N^2) is really heavy
  - Nested Loop is great for ranging process

{% highlight SQL %}
SELECT TOP 5 *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/87.jpg"><img src="/assets/img/posts/mssql_baseball/87.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

* Nested Loop in C#
{% highlight C# %}
  using System;
  using System.Collections.Generic;

  namespace NestedLoop
  {
    class Player
    {
      public int playerId;
    }

    class Salary
    {
      public int playerId;
    }

    class Program
    {
      static void Main(string[] args)
      {
        Random rand = new Random();

        // N
        List<Player> players = new List<Player>();
        for(int i=0; i<1000; i++)
        {
          if (rand.Next(0, 2) == 0)
            continue;

          players.Add(new Player() { playerId = i });
        }

        /* N
        List<Salary> salaries = new List<Salary>();
        for (int i = 0; i < 1000; i++)
        {
          if (rand.Next(0, 2) == 0)
            continue;

          salaries.Add(new Salary() { playerId = i });
        }
        ////////////////////////////////////////////////*/
        
        Dictionary<int, Salary> salaries = new Dictionary<int, Salary>();
        for (int i = 0; i < 1000; i++)
        {
          if (rand.Next(0, 2) == 0)
            continue;

          salaries.Add(i, new Salary() { playerId = i });
        }

        // Nested Loop
        List<int> result = new List<int>();
        foreach (Player p in players)
        {
          /* foreach(Salary s in salaries)
          {
            if(s.playerId == p.playerId)
            {
              result.Add(p.playerId);
              break;
            }
          } 
          ////////////////////////////////////////////////*/

          Salary s = null;
          if (salaries.TryGetValue(p.playerId, out s))
          {
            result.Add(p.playerId);
            if (result.Count == 5)
              break;
          }              
        }
      }
    }
  }
{% endhighlight %}

* Forcing to use Nested Loop
  - use `OPTION(LOOP JOIN)` to force using Nested Loop

{% highlight SQL %}
SELECT *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID
	OPTION(LOOP JOIN);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/89.jpg"><img src="/assets/img/posts/mssql_baseball/89.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

  - use `OPTION(FORCE ORDER)` to force ordering
{% highlight SQL %}
SELECT *
FROM players AS p
	INNER JOIN salaries AS s
	ON p.playerID = s.playerID
	OPTION(FORCE ORDER, LOOP JOIN);
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/90.jpg"><img src="/assets/img/posts/mssql_baseball/90.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>

## Hash

{% highlight SQL %}
SELECT *
FROM salaries AS s
	INNER JOIN teams AS t
	ON s.teamID = t.teamID;
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/mssql_baseball/88.jpg"><img src="/assets/img/posts/mssql_baseball/88.jpg"></a>
  <figcaption>MSSQL Baseball</figcaption>
</figure>



[Download](https://github.com/leehuhlee/Database){: .btn}
