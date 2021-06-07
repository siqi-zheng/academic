---
title: 'Learning SQL Notes #9: Conditional Logic'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The ninth note from Learning SQL"

date: "2021-06-07T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [What Is Conditional Logic?](#what-is-conditional-logic)
  - [The case Expression](#the-case-expression)
    - [Searched case Expressions](#searched-case-expressions)
    - [Simple case Expressions (A less flexible ver. of the previous expression)](#simple-case-expressions-a-less-flexible-ver-of-the-previous-expression)
  - [Examples of case Expressions](#examples-of-case-expressions)
    - [Result Set Transformations](#result-set-transformations)
    - [Checking for Existence](#checking-for-existence)
    - [(Avoid) Division-by-Zero Errors](#avoid-division-by-zero-errors)
    - [Conditional Updates](#conditional-updates)
    - [Handling Null Values](#handling-null-values)

# What Is Conditional Logic?

Conditional logic is simply the ability to take one of several paths during program execution.

Analogous to if-else in Python and R.

<PRE>
SELECT first_name, last_name, 
  <B>CASE</B> 
      WHEN active = 1 THEN 'ACTIVE'
    ELSE 'INACT
  <B>END</B> activity_type 
FROM customer;
</PRE>

## The case Expression

* The case expression is part of the SQL standard (SQL92 release) and has been implemented by Oracle Database, SQL Server, MySQL, PostgreSQL, IBM UDB, and others.
* case expressions are built into the SQL grammar and can be included in select, insert, update, and delete statements.

### Searched case Expressions

```sql
CASE 
  WHEN category.name IN ('Children','Family','Sports','Animation') 
    THEN 'All Ages'
  WHEN category.name = 'Horror' 
    THEN 'Adult'
  WHEN category.name IN ('Music','Games') 
    THEN 'Teens' 
  ELSE 'Other'
END
```

<PRE>
SELECT c.first_name, c.last_name, 
CASE 
  WHEN active = 0 THEN 0 
  <B>ELSE 
    (SELECT count(*) FROM rental r 
    WHERE r.customer_id = c.customer_id)</B>
  END num_rentals /*Create new variables*/
FROM customer c; 
</PRE>

### Simple case Expressions (A less flexible ver. of the previous expression)

<PRE>
CASE <B>V0</B> 
  WHEN V1 THEN E1 
  WHEN V2 THEN E2 ...
  WHEN VN THEN EN
  [ELSE ED] 
END
</PRE>

V0 represents a value, and the symbols V1, V2, ..., VN rep‐ resent values that are to be compared to V0.

## Examples of case Expressions

### Result Set Transformations

```sql
SELECT monthname(rental_date) rental_month, 
  count(*) num_rentals 
FROM rental
WHEN WHERE rental_date BETWEEN '2005-05-01' AND '2005-08-01'
GROUP BY monthname(rental_date);
```

| rental_month | num_rentals |
| :----------- | ----------: |
| May          |        1156 |
| June         |        2311 |
| July         |        6709 |
 
```sql
SELECT 
  SUM(CASE WHEN monthname(rental_date) = 'May' THEN 1 
        ELSE 0 END) May_rentals,
  SUM(CASE WHEN monthname(rental_date) = 'June' THEN 1 
        ELSE 0 END) June_rentals,
  SUM(CASE WHEN monthname(rental_date) = 'July' THEN 1 
        ELSE 0 END) July_rentals 
FROM rental
WHERE rental_date BETWEEN '2005-05-01' AND '2005-08-01';
```

| May_rentals | June_rentals | July_rentals |
| :---------- | ------------ | -----------: |
|        1156 |         2311 |         6709 |

When the monthname() function returns the desired value for that column, the case expression returns the value 1; otherwise, it returns a 0. When summed over all rows, each column returns the number of accounts opened for that month. Obviously, such transformations are practical for only a small number of values

### Checking for Existence

Sometimes you will want to determine whether a relationship exists between two entities **without regard for the quantity**. 

```sql
SELECT a.first_name, a.last_name, 
 CASE 
   WHEN EXISTS (SELECT 1 FROM film_actor fa
                  INNER JOIN film f ON fa.film_id = f.film_id 
                WHERE fa.actor_id = a.actor_id 
                  AND f.rating = 'G') THEN 'Y'
ELSE 'N'
 END g_actor
FROM actor a 
WHERE a.last_name LIKE 'S%' OR a.first_name LIKE 'S%';
```

### (Avoid) Division-by-Zero Errors

```sql
...
sum(p.amount) / 
  CASE WHEN count(p.amount) = 0 THEN 1 
    ELSE count(p.amount)
  END avg_payment
...
```

### Conditional Updates

```sql
UPDATE customer 
SET active = 
  CASE
    WHEN 90 <= (SELECT datediff(now(), max(rental_date))           
                FROM rental r
                WHERE r.customer_id = customer.customer_id)
      THEN 0 
    ELSE 1 
  END
WHERE active = 1;
/*if the number returned by the subquery is 90 or higher, the customer is marked as inactive.*/
```

### Handling Null Values

```sql
...
CASE 
  WHEN a.address IS NULL THEN 'Unknown' 
  ELSE a.address
END address,
...
```

Note: For calculations, null values often cause a null result. When performing calculations, case expressions are useful for translating a null value into a number (usually 0 or 1) that will allow the calculation to yield a non-null value.