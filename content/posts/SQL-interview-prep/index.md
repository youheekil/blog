---
title: "SQL Interview Preparation"
date: 2022-05-23
draft: true
author: Youhee
images: []
categories: ["Data Engineer"]

lightgallery: true

toc:
  auto: false
math:
  enable: true
---

## Database 
* `Database` is a **container** that holds all your tables. 
* A `table` is a container for a subset of your **data**. 
    - A table holds data organized in ***rows*** and ***columns*** 

```sql
CREATE DATABASE MovieIndustry;

CREATE DATABASE IF NOT EXISTS MovieIndustry;

SHOW DATABASES; /* The output lists all the databases in the system */

DROP DATABASE MovieIndustry; 
```

## Data Type 
* **Numeric**: INT, BIGINT, TINYINT, DECIMAL
* **Date and Time**: DATE, TIME, TIMESTAMP, YEAR
* **String**: VARCHAR, CHAR, ENUM, SET, BLOB
* **JSON**: JSON
* **Spatial Data**: GEOMETRY, POINT


## Practice SQL Using Real Dataset

* Getting data
```sql

-- Create table for OLYMPICS_HISTORY; 
DROP TABLE IF EXISTS OLYMPICS_HISTORY; 
CREATE TABLE IF NOT EXISTS OLYMPICS_HISTORY
(
  id          INT, 
  name        VARCHAR, 
  sex         VARCHAR, 
  age         VARCHAR, 
  height      VARCHAR, 
  weight      VARCHAR, 
  team        VARCHAR, 
  noc         VARCHAR,
  games       VARCHAR, 
  year        INT, 
  session     VARCHAR, 
  city        VARCHAR, 
  sport       VARCHAR, 
  event       VARCHAR, 
  medal       VARCHAR
)


-- Create table for OLYMPIC_NOCS; 
DROP TABLE IF EXISTS OLYMPICS_NOCS; 
CREATE TABLE IF NOT EXISTS OLYMPICS_NOCS
(
  noc         VARCHAR,
  region	  VARCHAR,
  notes		  VARCHAR
)


```

### QUERY
* 1. How many olympics games have been held?

```sql 
SELECT COUNT(DISTINCT games) AS total_olympics_games
FROM olympics_history ;
```
* 2. List down all Olympics games held so far.
```sql
SELECT DISTINCT oh.year, oh.session, oh.city
FROM olympics_history oh
ORDER BY oh.year; 
```

* 3. Mention the total no of nations who participated in each olympics game?
```sql
-- noc: country name abbreviation
-- olympics_nocs : noc = olympics_history.noc => olympics_nocs.region
WITH olympics_data AS (
	SELECT games, onoc.region
	FROM olympics_history oh
	INNER JOIN olympics_nocs onoc
	ON oh.noc = onoc.noc
	GROUP BY games, onoc.region
)
SELECT games, COUNT(region) AS total_countries
FROM olympics_data
GROUP BY games
ORDER BY games; 
```

* 4. Which year saw the highest and lowest no of countries participating in olympics

```SQL
-- data: the year of participating countries 
-- CONCAT (FIRSTVALUE(col) OVER(ORDER BY col) as lowest_number_of_country)
-- CONCAT (FIRSTVALUE(col) OVER(ORDER BY col DESC) as highest_number_of_country)


WITH 
	olympics_data AS (
		SELECT oh.games, onc.region
		FROM olympics_history oh
		INNER JOIN olympics_nocs onc 
		ON oh.noc = onc.noc
		GROUP BY oh.games, onc.region),

	tot_countries AS(
		SELECT games, COUNT(region) AS total_countries
		FROM olympics_data od
		GROUP BY od.games)
		
SELECT DISTINCT
      CONCAT(
		  first_value(games) over(order by total_countries), ' - ',
		  first_value(total_countries) over(order by total_countries)) as Lowest_Countries, 
	CONCAT(
		  first_value(games) over(order by total_countries DESC), ' - ',
		  first_value(total_countries) over(order by total_countries DESC)) as Highest_Countries
FROM tot_countries; 
```
* 5. Which nation has participated in all of the olympic games

```SQL
-- * 5. Which nation has participated in all of the olympic games
-- count( total number of olympics) -> 
-- count( countries' olympic participations) -> 
-- JOIN TWO TABLE to get the nation has participated in all of the olympic games

WITH olympic_data AS (
	SELECT oh.games, onc.region
	FROM olympics_history oh 
	INNER JOIN olympics_nocs onc
	ON oh.noc = onc.noc
	GROUP BY oh.games, onc.region
),
	total_olympics AS (
	SELECT COUNT(DISTINCT od.games) AS total_games
	FROM olympic_data od
), 
	countries_participated AS (
	SELECT od.region, COUNT(DISTINCT od.games) AS total_participated_games
	FROM olympic_data od
	GROUP BY od.region
)

SELECT cp.*
FROM countries_participated cp
JOIN total_olympics tg 
ON tg.total_games = cp.total_participated_games
ORDER BY 1;
```

```sql
/*6. Identify the sport which was played in all summer olympics.*/

/* Q7. Which Sports were just played only once in the olympics. 

count games group by sport  
filter to num_of_games = 1 
*/ 
WITH t1 AS (
	SELECT sport, COUNT(DISTINCT games) AS num_of_games
	FROM olympics_history 
	GROUP BY sport 
	HAVING COUNT(DISTINCT games) = 1
), 
	 t2 AS (
	SELECT sport, games
	FROM olympics_history
	GROUP BY sport, games
) 

SELECT t1.sport, t1.num_of_games, t2.games
FROM t1
INNER JOIN t2 ON t1.sport = t2.sport; 

-- Q11. Fetch the top 5 athletes who have won the most gold medals.
WITH t1 as 
	(
		SELECT name, count(medal) as total_of_gold
		FROM olympics_history
		WHERE medal = 'Gold'
		GROUP BY name
		ORDER BY COUNT(1) DESC
	), 
	t2 as 
		(SELECT *, DENSE_RANK() OVER (ORDER BY total_of_gold DESC) AS rnk
		FROM t1)
SELECT *
FROM t2
WHERE rnk <= 5;

-- Q12. Fetch the top 5 athletes who have won the most medals (gold/silver/bronze).
WITH t1 AS 
(
	SELECT name, team, count(medal) AS total_medal
	FROM olympics_history OH
	WHERE medal <> 'NA'
	GROUP BY name, team
), 
	t2 AS 
(
	SELECT *, DENSE_RANK() OVER(ORDER BY total_medal DESC) AS rnk
	FROM t1
)
SELECT name, team, total_medal
FROM t2
WHERE rnk <= 5; 

/* Q14. List down total gold, silver, and bronze medals won by each country. 

1. filter out the data where medal is 'NA'
2. group by the country with noc (join the data with olympics_nocs)
3. use crossfunction (create extension tablefunc;) to transform row level to column level 
*/ 
SELECT country 
, COALESCE(Gold, 0) AS Gold
, COALESCE(Silver, 0) AS Silver
, COALESCE(Bronze, 0) AS Bronze

FROM crosstab(
	'SELECT ONCS.region AS country, OH.medal, COUNT(1) AS total_medals
	FROM olympics_history OH
	JOIN olympics_nocs ONCS
	ON ONCS.noc = OH.noc
	WHERE OH.medal <> ''NA''
	GROUP BY ONCS.region, OH.medal
	ORDER BY ONCS.region, OH.medal', 
	'values (''Bronze''), (''Gold''), (''Silver'')')
	AS RESULT (country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
ORDER BY Gold DESC, Silver DESC, Bronze DESC
	
; 

/* Q16. Identify which country won the most gold, most silver, most bronze medals 
in each olympics game 
SUBSTRING(games_country, 1, POSITION(' - ' IN games_country) - 1) AS games
, SUBSTRING(games_country, POSITION(' - ' IN games_country) + 3) AS country*/

WITH temp AS(
	SELECT SUBSTRING(country, 1, POSITION(' - ' IN country) - 1) AS games
	, SUBSTRING(country, POSITION(' - ' IN country) + 3) AS country
	, COALESCE(Gold, 0) AS Gold
	, COALESCE(Silver, 0) AS Silver
	, COALESCE(Bronze, 0) AS Bronze
	FROM crosstab(
		'SELECT CONCAT(games, '' - '', ONCS.region) AS country, medal, COUNT(1) AS total_medals
		FROM olympics_history OH
		JOIN olympics_nocs ONCS
		ON ONCS.noc = OH.noc
		WHERE OH.medal <> ''NA''
		GROUP BY games, ONCS.region, medal
		ORDER BY games, ONCS.region, medal', 
		'values (''Bronze''), (''Gold''), (''Silver'')')
		AS RESULT (country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
)
SELECT DISTINCT games
, CONCAT(FIRST_VALUE(country) OVER(PARTITION BY games ORDER BY gold DESC), ' - ', FIRST_VALUE(gold) OVER(PARTITION BY games ORDER BY gold DESC)) AS max_gold
, CONCAT(FIRST_VALUE(country) OVER(PARTITION BY games ORDER BY silver DESC), ' - ', FIRST_VALUE(silver) OVER(PARTITION BY games ORDER BY silver DESC)) AS max_silver
, CONCAT(FIRST_VALUE(country) OVER(PARTITION BY games ORDER BY bronze DESC), ' - ', FIRST_VALUE(bronze) OVER(PARTITION BY games ORDER BY bronze DESC)) AS max_bronze
FROM temp
ORDER BY games; 

/*Q17. Most medal */

WITH temp AS(
	SELECT SUBSTRING(country, 1, POSITION(' - ' IN country) - 1) AS games
	, SUBSTRING(country, POSITION(' - ' IN country) + 3) AS country
	, COALESCE(Gold, 0) AS Gold
	, COALESCE(Silver, 0) AS Silver
	, COALESCE(Bronze, 0) AS Bronze
	FROM crosstab(
		'SELECT CONCAT(games, '' - '', ONCS.region) AS country, medal, COUNT(1) AS total_medals
		FROM olympics_history OH
		JOIN olympics_nocs ONCS
		ON ONCS.noc = OH.noc
		WHERE OH.medal <> ''NA''
		GROUP BY games, ONCS.region, medal
		ORDER BY games, ONCS.region, medal', 
		'values (''Bronze''), (''Gold''), (''Silver'')')
		AS RESULT (country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
)
SELECT * FROM temp; 


/* 6. Identify the sport which was played in all summer olympics.

Problem Statement: SQL query to fetch the list of all sports which have been part of every olympics. 
 
*/
WITH t1 AS (
	-- Total summer olympics games
	SELECT COUNT(DISTINCT games) AS total_games
	FROM olympics_history 
	WHERE session = 'Summer' 
), 
	t2 AS (
	-- number of games each sport played 
	SELECT sport, COUNT(DISTINCT games) AS num_of_games
	FROM olympics_history
	WHERE session = 'Summer'
	GROUP BY sport
	ORDER BY num_of_games DESC)
	
	SELECT *
	FROM t2
	JOIN t1 ON t1.total_games = t2.num_of_games;

-- Q.14 List down total gold, silver and bronze medals won by each country.
-- activate the tablefun extension by using 'CREATE EXTENSION IF NOT EXISTS tablefunc;'
SELECT country
	   , COALESCE(gold, 0) AS gold
	   , COALESCE(silver, 0) AS silver
	   , COALESCE(bronze, 0) AS bronze
FROM crosstab(
	$$
	SELECT ONC.region AS country, medal, COUNT(medal) AS num_of_medal
	FROM olympics_history OH
	JOIN olympics_nocs ONC ON OH.noc = ONC.noc
	WHERE medal <> 'NA'
	GROUP BY ONC.region, medal
	ORDER BY ONC.region, OH.medal;
	$$, 'values (''Bronze''), (''Gold''), (''Silver'')') 
	-- Because we used ORDER BY the medal 
	AS RESULT (Country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
ORDER BY gold DESC, silver DESC, bronze	DESC
;

-- Q.15 
WITH temp AS (
	SELECT 
	  SUBSTRING(country, 1, POSITION(' - ' IN country) - 1) AS games
	, SUBSTRING(country, POSITION(' - ' IN country) + 3) AS country
	, COALESCE(gold, 0) AS gold
	, COALESCE(silver, 0) AS silver
	, COALESCE(bronze, 0) AS bronze
	FROM crosstab
		($$
			SELECT CONCAT(games, ' - ', ONCS.region) AS country, medal, COUNT(1) AS total_medals
			FROM olympics_history OH
			JOIN olympics_nocs ONCS
			ON ONCS.noc = OH.noc
			WHERE OH.medal <> 'NA'
			GROUP BY games, ONCS.region, medal
			ORDER BY games, ONCS.region, medal;
		$$, 'values (''Bronze''), (''Gold''), (''Silver'')'
		) AS RESULT (Country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
	ORDER BY gold, silver, bronze
) 
SELECT * 
FROM temp
ORDER BY games, country, gold, silver, bronze; 


-- Q16. Identify which country won the most gold, most silver and most bronze medals in each olympic games.

CREATE EXTENSION IF NOT EXISTS tablefunc;

WITH temp AS (
	SELECT 
		SUBSTRING(country, 1, POSITION(' - ' IN country) - 1) AS games
		, SUBSTRING(country, POSITION(' - ' IN country) + 3) AS country
		, COALESCE(gold, 0) AS gold
		, COALESCE(silver, 0) AS silver
		, COALESCE(bronze, 0) AS bronze
	FROM crosstab(
		$$
		SELECT CONCAT(games, ' - ', onc.region) AS country, medal, COUNT(medal) AS num_of_medal
		FROM olympics_history oh
		JOIN olympics_nocs onc ON oh.noc = onc.noc
		WHERE medal <> 'NA'
		GROUP BY games, country, medal
		ORDER BY games, country, medal;
		$$, 'values (''Bronze''), (''Gold''), (''Silver'')'
		) AS RESULT (Country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
) 
-- window function to know which country won the most gold, most silver and most bronze 

SELECT 
	DISTINCT games 
	, CONCAT(FIRST_VALUE(country) OVER(PARTITION BY games ORDER BY gold DESC), ' - ', FIRST_VALUE(gold) OVER(PARTITION BY games ORDER BY gold DESC)) AS max_gold
	, CONCAT(FIRST_VALUE(country) OVER(PARTITION BY games ORDER BY silver DESC), ' - ', FIRST_VALUE(silver) OVER(PARTITION BY games ORDER BY silver DESC)) AS max_silver
	, CONCAT(FIRST_VALUE(country) OVER(PARTITION BY games ORDER BY bronze DESC), ' - ', FIRST_VALUE(bronze) OVER(PARTITION BY games ORDER BY bronze DESC)) AS max_bronze
FROM temp 
ORDER BY games;

-- Q17. Identify which country won the most gold, most silver, most bronze medals and the most medals in each olympic games.
CREATE EXTENSION IF NOT EXISTS tablefunc; 
WITH temp AS (
	SELECT 
		SUBSTRING(country, 1, POSITION('-' IN country) - 1) AS games
		, SUBSTRING(country, POSITION('-' IN country) + 1) AS country
		, COALESCE(gold, 0) AS gold
		, COALESCE(silver, 0) AS silver
		, COALESCE(bronze, 0) AS bronze
	FROM crosstab
	($$
		SELECT CONCAT(games, '-', onc.region) AS country, medal, COUNT(medal) AS num_of_medal
		FROM olympics_history oh
		JOIN olympics_nocs onc ON onc.noc = oh.noc 
		WHERE medal <> 'NA'
		GROUP BY games, onc.region, medal
		ORDER BY games, onc.region, medal; 
	$$, 'values (''Bronze''), (''Gold''), (''Silver'')') 
		
	AS RESULT (country VARCHAR, Bronze BIGINT, Gold BIGINT, Silver BIGINT)
)
```
