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

- [Cross Join (Cartesian Product)](#cross-join-cartesian-product)
- [Inner Joins](#inner-joins)
- [Joining Three or More Tables](#joining-three-or-more-tables)
- [Using Subqueries as Tables](#using-subqueries-as-tables)
- [Using the Same Table Twice](#using-the-same-table-twice)
- [Self-Joins](#self-joins)
- [Outer Joins](#outer-joins)
  - [Three-Way Outer Joins](#three-way-outer-joins)
- [Natural Joins](#natural-joins)


Join instructs the server to use a column as the *transportation* between tables, thus allows columns from both tables to be included in the query’s result set. 

## Cross Join (Cartesian Product)

If the query didn’t specify how the two tables should be joined, the database server generated the *Cartesian
product*, which is **every permutation** of the two tables.  

```sql
JOIN b
CROSS JOIN b
```
**R** codes: 
```r
merge(x = df1, y = df2, by = NULL)

library(data.table)
CJ(a, b)
```

Can be used to create a list of consecutive numbers.

## Inner Joins

If a value exists for the address_id column in one table but *not* the other, then the join fails for the rows containing that value, and those rows are **excluded** from the result set. Inner join only returns rows that satisfy the **join condition**.

<pre>
INNER JOIN b
  <b>ON a.id=b.id</b>
</pre>

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

See subquery notes.

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

## Outer Joins

```sql
SELECT f.film_id, f.title, count(i.inventory_id) num_copies  
FROM film f  
LEFT OUTER JOIN inventory i  
 ON f.film_id = i.film_id
GROUP BY f.film_id, f.title;
```

* Left outer join includes all rows from the table on the left side of the join (film, in this case) and then include columns from the table on the right side of the join (inventory) if the join is successful.

* The num_copies column definition was changed from count(*) to count(i.inventory_id), which will count the number of non-null values of the inventory.inventory_id column.

* A left outer join B $\equiv$ B right outer join A.

### Three-Way Outer Joins

<pre>
SELECT f.film_id, f.title, i.inventory_id, r.rental_date 
FROM film f  LEFT OUTER JOIN inventory i 
 ON f.film_id = i.film_id 
<b>LEFT OUTER JOIN rental r 
 ON i.inventory_id = r.inventory_id</b>
WHERE f.film_id BETWEEN 13 AND 15;
</pre>

## Natural Joins 

Lets the database server determine what the join conditions need to be.

```sql
SELECT c.first_name, c.last_name, date(r.rental_date) 
FROM customer c 
NATURAL JOIN rental r; 
```
Empty set (0.04 sec)

Because you specified a natural join, the server inspected the table definitions and added the join condition r.customer_id = c.customer_id to join the two tables. This would have worked fine, but in the Sakila schema all of the tables include the column last_update to show when each row was last modified, so the server is also adding the join condition r.last_update = c.last_update, which causes the query to return no data.

The only way around this issue is to use a subquery to restrict the columns for at least one of the tables:

```sql 
SELECT cust.first_name, cust.last_name, date(r.rental_date) 
 FROM 
 (SELECT customer_id, first_name, last_name 
 FROM customer 
 ) cust
NATURAL JOIN rental r;
```