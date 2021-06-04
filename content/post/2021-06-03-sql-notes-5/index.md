---
title: 'Learning SQL Notes #5: Querying Multiple Tables (CH. 5)'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The fifth note from Learning SQL"

date: "2021-06-03T20:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Join (Cartesian Product)](#join-cartesian-product)
- [Inner Joins](#inner-joins)
- [Joining Three or More Tables](#joining-three-or-more-tables)
- [Using Subqueries as Tables](#using-subqueries-as-tables)
- [Using the Same Table Twice](#using-the-same-table-twice)
- [Self-Joins](#self-joins)


Join instructs the server to use a column as the *transportation* between tables, thus allows columns from both tables to be included in the query’s result set. 

## Join (Cartesian Product)

If the query didn’t specify how the two tables should be joined, the database server generated the *Cartesian
product*, which is **every permutation** of the two tables.  
```sql
JOIN b
```
**R** codes: 
```r
merge(x = df1, y = df2, by = NULL)

library(data.table)
CJ(a, b)
```


## Inner Joins

If a value exists for the address_id column in one table but *not* the other, then the join fails for the rows containing that value, and those rows are **excluded** from the result set.

```sql
INNER JOIN b
  ON a.id=b.id
```
**R** codes: 
```r
merge(df1, df2, by = "id")

library(plyr)
join(df1, df2,
     type = "inner")
```

## Joining Three or More Tables

Join order is not important!

Force order:
```sql
SELECT STRAIGHT_JOIN COL1
```

## Using Subqueries as Tables

## Using the Same Table Twice

Either one of the actors in the movie:

```SQL
SELECT f.title 
FROM film f 
INNER JOIN film_actor fa 
  ON f.film_id = fa.film_id
INNER JOIN actor a 
  ON fa.actor_id = a.actor_id
WHERE ((a.first_name = 'CATE' AND a.last_name = 'MCQUEEN')
  OR (a.first_name = 'CUBA' AND a.last_name = 'BIRCH');
```

If we want movies that have both, you cannot simply replace OR with AND since this will return an empty set. Hence instead, you need to join the table twice:

```SQL
SELECT f.title
FROM film f 
/*once: */
INNER JOIN film_actor fa1 
  ON f.film_id = fa1.film_id
INNER JOIN actor a1
  ON fa1.actor_id = a1.actor_id 
/*twice: */
INNER JOIN film_actor fa2 
  ON f.film_id = fa2.film_id
INNER JOIN actor a2 
  ON fa2.actor_id = a2.actor_id
/*filter condition is applied*/
WHERE (a1.first_name = 'CATE' AND a1.last_name = 'MCQUEEN') 
  AND (a2.first_name = 'CUBA' AND a2.last_name = 'BIRCH');
```

## Self-Joins

Some tables include a self-referencing foreign key, which means that it includes a column that points to the primary key within the same table.

Imagine that the film table includes the column prequel_film_id, which points to the film’s parent (e.g., the film Fiddler Lost II would use this column to point to the parent film Fiddler Lost).

Using a self-join, you can write a query that lists every film that has a prequel, along with the prequel’s title:
```sql
SELECT f.title, f_prnt.title prequel 
FROM film f
INNER JOIN film f_prnt
  ON f_prnt.film_id = f.prequel_film_id 
WHERE f.prequel_film_id IS NOT NULL;
```

A possible outcome:

 | title           |      prequel |
 | :-------------- | -----------: |
 | FIDDLER LOST II | FIDDLER LOST |