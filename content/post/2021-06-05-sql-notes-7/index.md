---
title: 'Learning SQL Notes #7: Grouping and Aggregates (CH. 8)'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The seventh note from Learning SQL"

date: "2021-06-05T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
--- 


- [Grouping Concepts](#grouping-concepts)
- [Aggregate Functions](#aggregate-functions)
- [Generating Groups](#generating-groups)
  - [Single-Column/Multicolumn Grouping](#single-columnmulticolumn-grouping)
  - [Grouping via Expressions](#grouping-via-expressions)
  - [Generating Rollups](#generating-rollups)
- [Group Filter Conditions](#group-filter-conditions)

## Grouping Concepts

```sql
SELECT customer_id, count(*) 
FROM rental
GROUP BY customer_id
HAVING count(*) >= 40
ORDER BY 2 DESC;
```
WARNING:

~~WHERE count(*) >= 40~~ since aggregate functions should come with `HAVING`.

**R** codes: 

```r
library(tidyverse)
rental %>%
  group_by(customer_id) %>%
  summarize(counts=n()) %>%
  filter(counts>=40) %>%
  arrange(desc(counts))
```


## Aggregate Functions

Some aggregate functions in SQL/R:

| SQL            |       R |
| :------------- | ------: |
| count()        | count() |
| sum()          |   sum() |
| average()      |  mean() |
| min()          |   min() |
| max()          |   max() |
| group_concat() | paste() |
| first()        |     [1] |
| last()         |    [-1] |

```sql
SELECT COUNT(DISTINCT col1) 
FROM string_tbl;
```

**R** codes: 

```r
length(unique(string_tbl$col1))
```

**NULLS are ignored unless you use `count(*)` where all rows will be counted.**

## Generating Groups

### Single-Column/Multicolumn Grouping

Grouping can be done on 1 or more columns with aggregate functions.

```sql
SELECT actor_id, count(*) 
FROM film_actor
GROUP BY actor_id;

SELECT fa.actor_id, f.rating, count(*) 
FROM film_actor fa 
INNER JOIN film f 
ON fa.film_id = f.film_id 
GROUP BY fa.actor_id, f.rating
ORDER BY 1,2;
```

**R** codes are analogous to the codes in the last section.


### Grouping via Expressions

```sql
SELECT extract(YEAR FROM rental_date) year, 
COUNT(*) how_many 
FROM rental
GROUP BY extract(YEAR FROM rental_date);
```

**R** codes: 

```r
library(tidyverse)
rental %>%
  mutate(year=year(rental_date)) %>%
  group_by(year) %>%
  summarize(counts=n()) %>%
```


### Generating Rollups

Find total counts for each distinct actor.

```sql
/*MySQL*/
SELECT fa.actor_id, f.rating, count(*)  
FROM film_actor fa  
INNER JOIN film f  
ON fa.film_id = f.film_id  
GROUP BY fa.actor_id, f.rating WITH ROLLUP
ORDER BY 1,2;

/*Oracle*/
GROUP BY ROLLUP(fa.actor_id, f.rating)
GROUP BY a, ROLLUP(b, c)
```

| actor_id | rating | count(*) |
| :------- | ------ | -------: |
| NULL     | NULL   |     5462 |
| 1        | NULL   |       19 |
| 1        | G      |        4 |
| 1        | PG     |        6 |
| 1        | PG-13  |        1 |
| 1        | R      |        3 |
| 1        | NC-17  |        5 |
| 2        | NULL   |       25 |
| 2        | G      |        7 |


**R** codes: 

```r
library(reshape2)
library(zoo)

m <- melt(df, measure.vars = "sales")
dout <- dcast(m, year + month + region ~ variable, fun.aggregate = sum, margins = "month")

dout$month <- na.locf(replace(dout$month, dout$month  == "(all)", NA))
```

See here: https://stackoverflow.com/questions/36169073/how-to-do-group-by-rollup-in-r-like-sql


## Group Filter Conditions

* `HAVING` with aggregate functions;
* `WHERE` with original columns;

![](2.gif)