---
title: 'Learning SQL Notes #12: Views'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The twelfth note from Learning SQL"

date: "2021-06-09T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

Well-designed applications generally expose a public interface while keeping imple‐ mentation details private, thereby enabling future design changes without impacting end users. When designing your database, you can achieve a similar result by keeping your tables private and allowing your users to access data only through a set of *views*.

# What Are Views?

```sql
CREATE VIEW customer_vw 
(customer_id, 
first_name, 
last_name, 
email
) 
AS
SELECT customer_id, 
first_name, 
last_name,
concat(substr(email,1,2), '*****', substr(email, -4)) email
FROM customer;

/*view the View*/
describe customer_vw;
/*group by, having, where, join etc. can also be used*/
```

# Why Use Views?

* Data Security

  Oracle Database users have another option for securing both rows and columns of a table: Virtual Private Database (VPD). VPD allows you to attach policies to your tables, after which the server will modify a user’s query as necessary to enforce the policies.

* Data Aggregation

  ```sql
  CREATE VIEW sales_by_film_category AS
  SELECT c.name AS category,
  SUM(p.amount) AS total_sales 
  FROM payment AS p
  INNER JOIN rental AS r 
  ON p.rental_id = r.rental_id 
  INNER JOIN inventory AS i 
  ON r.inventory_id = i.inventory_id 
  INNER JOIN film AS f 
  ON i.film_id = f.film_id 
  INNER JOIN film_category AS fc 
  ON f.film_id = fc.film_id 
  INNER JOIN category AS c 
  ON fc.category_id = c.category_id 
  GROUP BY c.name
  ORDER BY total_sales DESC;
  ```

  You have great flexibility! You can create a film_category_sales table, load it with aggregated data, and modify the sales_by_film_category view definition to retrieve data from this table if this improves the performance significantly.

* Hiding Complexity

  One of the most common reasons for deploying views is to shield end users from complexity. 

  ```sql
  CREATE VIEW film_stats AS
  SELECT f.film_id, f.title, f.description, f.rating,
  (SELECT c.name 
  FROM category c
  INNER JOIN film_category fc 
  ON c.category_id = fc.category_id 
  WHERE fc.film_id = f.film_id) category_name, 
  (SELECT count(*) 
  FROM film_actor fa
  WHERE fa.film_id = f.film_id ) num_actors, 
  (SELECT count(*) 
  FROM inventory i
  WHERE i.film_id = f.film_id ) inventory_cnt, 
  (SELECT count(*) 
  FROM inventory i 
  INNER JOIN rental r
  ON i.inventory_id = r.inventory_id 
  WHERE i.film_id = f.film_id ) num_rentals
  FROM film f;
  ```

  If someone uses this view but does not reference the category_name, num_actors, inventory_cnt, or num_rentals column, then none of the subqueries will be executed. This approach allows the view to be used for supplying descriptive information from the film table without unnecessarily joining five other tables.

* Joining Partitioned Data
  
  Some database designs break large tables into multiple pieces in order to improve performance. For example, if the payment table became large, the designers may decide to break it into two tables: payment_current, which holds the latest six months of data, and payment_historic, which holds all data up to six months ago. You can make it look like all payment data is stored in a single table.

  ```sql
  CREATE VIEW payment_all 
  (payment_id, 
  customer_id, 
  staff_id, 
  rental_id, amount, 
  payment_date, 
  last_update
  ) AS
  SELECT payment_id, customer_id, staff_id, rental_id, amount, payment_date, last_update 
  FROM payment_historic 
  UNION ALL
  SELECT payment_id, customer_id, staff_id, rental_id, amount, payment_date, last_update
  FROM payment_current;
  ```

  Using a view in this case is a good idea because it allows the designers to change the structure of the underlying data without the need to force all database users to modify their queries.

# Updatable Views 

In the case of MySQL, a view is updatable if the following conditions are met:

* No aggregate functions are used (max(), min(), avg(), etc.). 
* The view does not employ group by or having clauses. 
* No subqueries exist in the select or from clause, and any subqueries in the where clause do not refer to tables in the from clause.
* The view does not utilize union, union all, or distinct. 
* The from clause includes at least one table or updatable view.
* The from clause uses only inner joins if there is more than one table or view.

## Updating Simple Views

```sql
UPDATE customer_vw 
SET last_name = 'SMITH-ALLEN'
WHERE customer_id = 1;
```

No`insert` for views that contain derived columns, even if the derived columns are not included in the statement. Cannot modify columns derived from an expression.

## Updating Complex Views

For complex views with more than one table, you are allowed to modify both of the underlying tables separately, but not within a single statement. In order to insert data through a complex view, you would need to know from where each column is sourced. Since many views are created to hide complexity from end users, this seems to defeat the purpose if the users need to have explicit knowledge of the view definition.