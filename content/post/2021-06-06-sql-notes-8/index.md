---
title: 'Learning SQL Notes #8: Subqueries'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The eighth note from Learning SQL"

date: "2021-06-06T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [What Is a Subquery?](#what-is-a-subquery)
- [Subquery Types](#subquery-types)
  - [Noncorrelated Subqueries](#noncorrelated-subqueries)
    - [Multiple-Row, Single-Column Subqueries](#multiple-row-single-column-subqueries)
      - [The in and not in operators](#the-in-and-not-in-operators)
      - [The all operator](#the-all-operator)
      - [The any operator (OR)](#the-any-operator-or)
    - [Multicolumn Subqueries](#multicolumn-subqueries)
  - [Correlated Subqueries](#correlated-subqueries)
    - [The exists Operator](#the-exists-operator)
    - [Data Manipulation Using Correlated Subqueries](#data-manipulation-using-correlated-subqueries)
- [When to Use Subqueries](#when-to-use-subqueries)
  - [Subqueries as Data Sources](#subqueries-as-data-sources)
    - [Data fabrication](#data-fabrication)
    - [Task-oriented subqueries](#task-oriented-subqueries)
    - [Common table expressions](#common-table-expressions)
  - [Subqueries as Expression Generators](#subqueries-as-expression-generators)
- [Subquery Wrap-Up](#subquery-wrap-up)

# What Is a Subquery?

A *subquery* is a query contained within another SQL statement (which I refer to as the containing statement for the rest of this discussion). A subquery is always enclosed within parentheses, and it is usually executed prior to the containing statement. Like any query, a subquery returns a result set that may consist of:

* A single row with a single column 
* Multiple rows with a single column
* Multiple rows having multiple columns

<pre>
SELECT customer_id, first_name, last_name 
FROM customer
WHERE customer_id = <b>(SELECT MAX(customer_id) FROM customer);</b>
</pre>

# Subquery Types

## Noncorrelated Subqueries

### Multiple-Row, Single-Column Subqueries

#### The in and not in operators

<pre>
SELECT city_id, city
FROM city
WHERE country_id <> <b>(SELECT country_id FROM country WHERE country = 'India');</b>
</pre>

Note: Subquery should not return more than one row when you use `WHERE` to filter a condition with inequality/equality in this case.

What you can do is use the following subqueries:

```sql
SELECT country_id 
FROM country
WHERE country IN ('Canada','Mexico');
```

or

```sql
SELECT country_id 
FROM country
WHERE country = 'Canada' OR country = 'Mexico';
```

in the following ways:

```sql
SELECT city_id, city  
FROM city
WHERE country_id IN  
(SELECT country_id  
FROM country
WHERE country IN ('Canada','Mexico'));
```

or the opposite:

```sql
SELECT city_id, city  
FROM city
WHERE country_id NOT IN  
(SELECT country_id  
FROM country
WHERE country IN ('Canada','Mexico'));
```

#### The all operator

The all operator allows you to make comparisons between a single value and every value in a set:

```sql
SELECT first_name, last_name  
FROM customer  
WHERE customer_id <> ALL  
(SELECT customer_id  
FROM payment
WHERE amount = 0);
```

or the equivalent:

```sql
SELECT first_name, last_name  
FROM customer  
WHERE customer_id NOT IN
(SELECT customer_id  
FROM payment
WHERE amount = 0);
```

**Any attempt to equate a value to null yields unknown, so when using `not in` or `<> all` to compare a value to a set of values, you must be careful to ensure that the set of values does not contain a null value.**

The subquery in this example returns the total number of film rentals for all custom‐ ers in North America, and the containing query returns all customers whose total
number of film rentals exceeds any of the North American customers.

```sql
SELECT customer_id, count(*)  
FROM rental
GROUP BY customer_id  
HAVING count(*) > ALL  
(SELECT count(*) 
FROM rental r        
INNER JOIN customer c
  ON r.customer_id = c.customer_id 
INNER JOIN address a
  ON c.address_id = a.address_id 
INNER JOIN city ct
  ON a.city_id = ct.city_id 
INNER JOIN country co
  ON ct.country_id = co.country_id
WHERE co.country IN ('United States','Mexico','Canada')  
GROUP BY r.customer_id
);
```

 #### The any operator (OR)

A condition using the any operator evaluates to true as soon as a single comparison is favorable.

```sql
SELECT customer_id, sum(amount)
FROM payment
GROUP BY customer_id  
HAVING sum(amount) > ANY  
(SELECT sum(amount) 
FROM payment p     
INNER JOIN customer c
  ON r.customer_id = c.customer_id 
INNER JOIN address a
  ON c.address_id = a.address_id 
INNER JOIN city ct
  ON a.city_id = ct.city_id 
INNER JOIN country co
  ON ct.country_id = co.country_id
WHERE co.country IN ('Bolivia','Paraguay','Chile')
GROUP BY co.country
);
```

### Multicolumn Subqueries

```sql
SELECT actor_id, film_id  
FROM film_actor  
WHERE (actor_id, film_id) IN  
(SELECT a.actor_id, f.film_id  
FROM actor a 
  CROSS JOIN film f
WHERE a.last_name = 'MONROE'
AND f.rating = 'PG');
```

## Correlated Subqueries

A *correlated* *subquery*, on the other hand, is *dependent* on its containing statement from which it references one or more columns. 

<pre>
SELECT c.first_name, c.last_name  
FROM customer c  
WHERE 20 =  
(SELECT count(*) 
FROM rental r
WHERE r.customer_id = <b>c.customer_id</b>);
/*customers who have rented exactly 20 films*/
</pre>

### The exists Operator

You use the exists operator when you want to identify that a relationship exists without regard for the quantity.

```sql
SELECT c.first_name, c.last_name  
FROM customer c  
WHERE (NOT) EXISTS  
(SELECT r.rental_date, r.customer_id, 'ABCD' str, 2 * 3 / 7 nmbr /*can be replaced by anything*/ 
FROM rental r  
WHERE r.customer_id = c.customer_id 
AND date(r.rental_date) < '2005-05-25');
```

**Since the condition in the containing query only needs to know how many rows have been returned, the actual data the subquery returned is irrelevant.**

### Data Manipulation Using Correlated Subqueries

```sql
UPDATE customer c 
SET c.last_update =
(SELECT max(r.rental_date) 
FROM rental r
WHERE r.customer_id = c.customer_id);

UPDATE customer c SET c.last_update =
(SELECT max(r.rental_date) FROM rental r WHERE r.customer_id = c.customer_id) WHERE EXISTS
(SELECT 1 FROM rental r
WHERE r.customer_id = c.customer_id);
/*executes only if the condition in the update statement’s where clause evaluates to true (meaning that at least one rental was found for the customer), thus protecting the data in the last_update column from being
overwritten with a null.*/

DELETE FROM customer WHERE 365 < ALL
(SELECT datediff(now(), r.rental_date) days_since_last_rental FROM rental r
WHERE r.customer_id = customer.customer_id);
/*removes rows from the customer table where there have been no film rentals in the past year*/
```

# When to Use Subqueries

## Subqueries as Data Sources

```sql
SELECT c.first_name, c.last_name,  pymnt.num_rentals, pymnt.tot_payments  
FROM customer c  
INNER JOIN     
(SELECT customer_id, count(*) num_rentals, sum(amount) tot_payments 
FROM payment
GROUP BY customer_id ) pymnt /*execute first*/
 ON c.customer_id = pymnt.customer_id;
```

### Data fabrication

First we have a table for some standards (small/average/heavy) with lower and upper bounds.

```sql
SELECT 'Small Fry' name, 0 low_limit, 74.99 high_limit  UNION ALL
SELECT 'Average Joes' name, 75 low_limit, 149.99 high_limit  
UNION ALL
SELECT 'Heavy Hitters' name, 150 low_limit, 9999999.99 high_limit;
```

Then we have transformed the original tables into the desired one.

```sql
SELECT pymnt_grps.name, count(*) num_customers  
FROM
(SELECT customer_id, count(*) num_rentals, sum(amount) tot_payments
FROM payment  
GROUP BY customer_id) pymnt  
INNER JOIN  (SELECT 'Small Fry' name, 0 low_limit, 74.99 high_limit  
UNION ALL  
SELECT 'Average Joes' name, 75 low_limit, 149.99 high_limit  
UNION ALL  
SELECT 'Heavy Hitters' name, 150 low_limit, 9999999.99 high_limit  ) pymnt_grps
 ON pymnt.tot_payments 
BETWEEN pymnt_grps.low_limit AND pymnt_grps.high_limit
GROUP BY pymnt_grps.name; 
```

### Task-oriented subqueries

```sql
SELECT c.first_name, c.last_name, ct.city,  
sum(p.amount) tot_payments, count(*) tot_rentals  
FROM payment p  
INNER JOIN customer c  
 ON p.customer_id = c.customer_id  
INNER JOIN address a  
 ON c.address_id = a.address_id  
INNER JOIN city ct  
 ON a.city_id = ct.city_id
GROUP BY c.first_name, c.last_name, ct.city;
```

We only need names/cities/addresses for display purpose only, so we can use subqueries to group the data first before joining other tables. A more efficient code chunk for the same task：

```sql
SELECT c.first_name, c.last_name,  ct.city,  pymnt.tot_payments, pymnt.tot_rentals  
FROM  (SELECT customer_id, count(*) tot_rentals, sum(amount) tot_payments
FROM payment  
GROUP BY customer_id) pymnt  
INNER JOIN customer c  
 ON pymnt.customer_id = c.customer_id  
INNER JOIN address a  
 ON c.address_id = a.address_id  
INNER JOIN city ct
 ON a.city_id = ct.city_id;
```

### Common table expressions 

```sql
WITH actors_s AS  
(SELECT actor_id, first_name, last_name  
FROM actor  
WHERE last_name LIKE 'S%'
) /*can be used in the subsequent queries*/
...
```

## Subqueries as Expression Generators

Correlated scalar subqueries. The customer table is accessed three times (once in each of the three subqueries) rather than just once. 

```sql
SELECT  (SELECT c.first_name 
FROM customer c  
WHERE c.customer_id = p.customer_id  ) first_name,  (SELECT c.last_name 
FROM customer c  
WHERE c.customer_id = p.customer_id  ) last_name,  (SELECT ct.city  
FROM customer c  
INNER JOIN address a 
 ON c.address_id = a.address_id
INNER JOIN city ct
 ON a.city_id = ct.city_id
WHERE c.customer_id = p.customer_id
) city,  
sum(p.amount) tot_payments, count(*) tot_rentals  
FROM payment p
GROUP BY p.customer_id; 
``` 

Similarly,

```sql
INSERT INTO film_actor (actor_id, film_id, last_update) VALUES (
(SELECT actor_id 
FROM actor
WHERE first_name = 'JENNIFER' AND last_name = 'DAVIS'), (SELECT film_id FROM film 
WHERE title = 'ACE GOLDFINGER'), 
now()
);
```

# Subquery Wrap-Up

* Return a single column and row, a single column with multiple rows, and multi‐ ple columns and rows
* Are independent of the containing statement (noncorrelated subqueries)
* Reference one or more columns from the containing statement (correlated subqueries)
* Are used in conditions that utilize comparison operators as well as the special-purpose operators in, not in, exists, and not exists
* Can be found in select, update, delete, and insert statements 
* Generate result sets that can be joined to other tables (or subqueries) in a query 
* Can be used to generate values to populate a table or to populate columns in a query’s result set
* Are used in the select, from, where, having, and order by clauses of queries

Happy learning!

![](2.jpg)