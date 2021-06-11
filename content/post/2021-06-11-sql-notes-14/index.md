---
title: 'Learning SQL Notes #14: Analytic Functions'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The fourteenth note from Learning SQL"

date: "2021-06-11T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Analytic Function Concepts](#analytic-function-concepts)
  - [Data Windows](#data-windows)
  - [Localized Sorting](#localized-sorting)
- [Ranking](#ranking)
  - [Ranking Functions](#ranking-functions)
  - [Generating Multiple Rankings](#generating-multiple-rankings)
- [Reporting Functions](#reporting-functions)
  - [Window Frames](#window-frames)
  - [Lag and Lead](#lag-and-lead)
  - [Column Value Concatenation](#column-value-concatenation)

# Analytic Function Concepts

## Data Windows

<pre>
SELECT quarter(payment_date) quarter, 
 monthname(payment_date) month_nm, 
 sum(amount) monthly_sales, 
 <b>max(sum(amount)) 
    over () max_overall_sales,</b>/*controlled by where and group by and return the highest monthly total payment in 2005*/
 <b>max(sum(amount))
    over (partition by quarter(payment_date)) max_qrtr_sales</b> /*controlled by where and group by and return the highest monthly total payment in each quarter in 2005*/
FROM payment 
WHERE year(payment_date) = 2005
GROUP BY quarter(payment_date), monthname(payment_date);
</pre>

The analytic functions used to generate these additional columns group rows into two different sets: one set containing all rows in the same quarter and another set containing all of the rows. To accommodate this type of analysis, analytic functions include the ability to group rows into *windows*, which effectively partition the data for use by the analytic function without changing the overall result set. Windows are defined using the `over` clause combined with an optional `partition` by subclause. In the previous query, both analytic functions include an over clause, but the first one is empty, indicating that the window should include the entire result set, whereas the second one specifies that the window should include only rows within the same quarter. Data windows may contain anywhere from a single row to all of the rows in the result set, and different analytic functions can define different data windows.

## Localized Sorting

<pre>
SELECT quarter(payment_date) quarter, 
 monthname(payment_date) month_nm, 
 sum(amount) monthly_sales, 
 <b>rank() over (order by sum(amount) desc)</b> sales_rank /* order by only controls the rank()*/
FROM payment
WHERE year(payment_date) = 2005
GROUP BY quarter(payment_date), monthname(payment_date)
ORDER BY 1, month(payment_date);/* order by only controls the presentation*/
</pre> 

or you may insert `partition by quarter(payment_date)` into the `over()` above to obtain rank within each quarter.

# Ranking

## Ranking Functions 

There are multiple ranking functions available in the SQL standard, with each one taking a different approach to how ties are handled: 

`row_number`

Returns a unique number for each row, with rankings arbitrarily assigned in case of a tie

`rank` 

Returns the same ranking in case of a tie, with gaps in the rankings 

`dense_rank`

Returns the same ranking in case of a tie, with no gaps in the rankings

<pre>
SELECT customer_id, count(*) num_rentals, 
 row_number() over (order by count(*) desc) row_number_rnk, 
 rank() over (order by count(*) desc) rank_rnk, 
 dense_rank() over (order by count(*) desc) dense_rank_rnk 
FROM rental
GROUP BY customer_id
ORDER BY 2 desc;
</pre>

| customer_id | num_rentals | row_number_rnk | rank_rnk | dense_rank_rnk |
| :---------- | ----------- | -------------- | -------- | -------------: |
| 144         | 42          | 3              | 3        |              3 |
| 236         | 42          | 4              | 3        |              3 |
| <b>75</b>   | <b>41</b>   | <b>5</b>       | <b>5</b> |       <b>4</b> |

To get back to the original request, how would you identify the top 10 customers? There are three possible solutions:

* Use the row_number function to identify customers ranked from 1 to 10, which results in exactly 10 customers in this example, but in other cases might exclude customers having the same number of rentals as the 10th ranked customer.

* Use the rank function to identify customers ranked 10 or less, which also results in exactly 10 customers.

* Use the dense_rank function to identify customers ranked 10 or less, which yields a list of 37 customers.

## Generating Multiple Rankings

<pre>
SELECT customer_id, 
 monthname(rental_date) rental_month, 
 count(*) num_rentals, 
 rank() over (<b>partition by monthname(rental_date) </b>
 order by count(*) desc) rank_rnk 
FROM rental
GROUP BY customer_id, monthname(rental_date)
ORDER BY 2, 3 desc;
</pre>

so that rank() starts from 1 for each month.

Looking at the results, you can see that the rankings are reset to 1 for each month. In order to generate the desired results for the marketing department (top five custom‐ ers from each month), you can simply wrap the previous query in a subquery and add a filter condition to exclude any rows with a ranking higher than five:

<pre>
SELECT customer_id, rental_month, num_rentals, rank_rnk ranking
FROM 
(SELECT customer_id,
monthname(rental_date) rental_month, count(*) num_rentals,
rank() over (partition by monthname(rental_date) order by count(*) desc) rank_rnk
FROM rental
GROUP BY customer_id, monthname(rental_date) 
) cust_rankings 
<b>WHERE rank_rnk <= 5</b>
ORDER BY rental_month, num_rentals desc, rank_rnk;
</pre>

Since analytic functions can be used only in the SELECT clause, you will often need to **nest queries** if you need to do any filtering or grouping based on the results from the analytic function.

<table>
  <thead>
    <tr>
      <th>Window Function</th>
      <th>Return Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>CUME_DIST()</td>
      <td>DOUBLE PRECISION</td>
      <td>The CUME_DIST() window function calculates the relative rank of the current row within a window partition: (number of rows preceding or peer with current row) / (total rows in the window partition)</td>
    </tr>
    <tr>
      <td>DENSE_RANK()</td>
      <td>BIGINT</td>
      <td>The DENSE_RANK () window function determines the rank of a value in a group of values based on the ORDER BY expression and the OVER clause. Each value is ranked within its partition. Rows with equal values receive the same rank. There are no gaps in the sequence of ranked values if two or more rows have the same rank.</td>
    </tr>
    <tr>
      <td>NTILE()</td>
      <td>INTEGER</td>
      <td>The NTILE window function divides the rows for each window partition, as equally as possible, into a specified number of ranked groups. The NTILE window function requires the ORDER BY clause in the OVER clause.</td>
    </tr>
    <tr>
      <td>PERCENT_RANK()</td>
      <td>DOUBLE PRECISION</td>
      <td>The PERCENT_RANK () window function calculates the percent rank of the current row using the following formula: (x - 1) / (number of rows in window partition - 1) where x is the rank of the current row.</td>
    </tr>
    <tr>
      <td>RANK()</td>
      <td>BIGINT</td>
      <td>The RANK window function determines the rank of a value in a group of values. The ORDER BY expression in the OVER clause determines the value. Each value is ranked within its partition. Rows with equal values for the ranking criteria receive the same rank. Drill adds the number of tied rows to the tied rank to calculate the next rank and thus the ranks might not be consecutive numbers. For example, if two rows are ranked 1, the next rank is 3. The DENSE_RANK window function differs in that no gaps exist if two or more rows tie.</td>
    </tr>
    <tr>
      <td>ROW_NUMBER()</td>
      <td>BIGINT</td>
      <td>The ROW_NUMBER window function determines the ordinal number of the current row within its partition. The ORDER BY expression in the OVER clause determines the number. Each value is ordered within its partition. Rows with equal values for the ORDER BY expressions receive different row numbers nondeterministically.</td>
    </tr>
  </tbody>
</table>

# Reporting Functions

Calculate total by month/by total

<pre>
SELECT monthname(payment_date) payment_month, 
 amount,
 <b>sum(amount) over (partition by monthname(payment_date)) monthly_total,
 sum(amount) over () grand_total </b>
FROM payment 
WHERE amount >= 10
ORDER BY 1;
</pre>

| payment_month | amount | monthly_total | grand_total |
| :------------ | ------ | ------------- | ----------: |
| August        | 10.99  | 521.53        |     1262.86 |
| August        | 11.99  | 521.53        |     1262.86 |

Calculate percentage:

<pre>
SELECT monthname(payment_date) payment_month, 
 amount,
 <b>round(sum(amount) / sum(sum(amount)) over () * 100, 2) pct_of_total</b>
FROM payment 
GROUP BY monthname(payment_date);
</pre>

| payment_month | month_total | pct_of_total |
| :------------ | ----------- | -----------: |
| May           | 4824.43     |         7.16 |
| June          | 9631.88     |        14.29 |
| July          | 28373.89    |        42.09 |
| August        | 24072.13    |        35.71 |
| February      | 514.18      |         0.76 |

Quasi-ranking functions:

<pre>
SELECT monthname(payment_date) payment_month, 
 sum(amount) month_total, 
 <b>CASE sum(amount) 
  WHEN max(sum(amount)) over () THEN 'Highest'
  WHEN min(sum(amount)) over () THEN 'Lowest'
  ELSE 'Middle'
 END descriptor</b>
FROM payment
GROUP BY monthname(payment_date);
</pre>

| payment_month | month_total |     descriptor |
| :------------ | ----------- | -------------: |
| May           | 4824.43     |         Middle |
| June          | 9631.88     |         Middle |
| July          | 28373.89    | <b>Highest</b> |
| August        | 24072.13    |         Middle |
| February      | 514.18      |  <b>Lowest</b> |

## Window Frames

<pre>
SELECT yearweek(payment_date) payment_week, 
 sum(amount) week_total, 
 sum(sum(amount)) 
  <b>over (order by yearweek(payment_date)
   rows unbounded preceding)</b> rolling_sum
FROM payment
GROUP BY yearweek(payment_date) 
ORDER BY 1;
</pre>

<pre>
SELECT yearweek(payment_date) payment_week, 
 sum(amount) week_total, 
 avg(sum(amount)) 
  over (order by yearweek(payment_date) 
  <b>rows between 1 preceding and 1 following</b>) rolling_3wk_avg 
FROM payment
GROUP BY yearweek(payment_date)
ORDER BY 1;
</pre>

<pre>
SELECT date(payment_date), sum(amount), 
 avg(sum(amount)) 
  over (order by date(payment_date) 
  <b>range between interval 3 day preceding and interval 3 day following</b>) range 
FROM payment
WHERE payment_date BETWEEN '2005-07-01' AND '2005-09-01'
GROUP BY date(payment_date)
ORDER BY 1;
</pre>

## Lag and Lead

<table>
  <thead>
    <tr>
      <th>Window Function</th>
      <th>Argument Type</th>
      <th>Return Type</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>LAG()</td>
      <td>Any supported Drill data types</td>
      <td>Same as the expression  type</td>
      <td>The LAG() window function returns the value for the row before the current row in a partition. If no row exists, null is returned.</td>
    </tr>
    <tr>
      <td>LEAD()</td>
      <td>Any supported Drill data types</td>
      <td>Same as the expression type</td>
      <td>The LEAD() window function returns the value for the row after the current row in a partition. If no row exists, null is returned.</td>
    </tr>
    <tr>
      <td>FIRST_VALUE</td>
      <td>Any supported Drill data types</td>
      <td>Same as the expression type</td>
      <td>The FIRST_VALUE window function returns the value of the specified expression with respect to the first row in the window frame.</td>
    </tr>
    <tr>
      <td>LAST_VALUE</td>
      <td>Any supported Drill data types</td>
      <td>Same as the expression type</td>
      <td>The LAST_VALUE window function returns the value of the specified expression with respect to the last row in the window frame.</td>
    </tr>
  </tbody>
</table>

<pre>
SELECT yearweek(payment_date) payment_week, 
 sum(amount) week_total, 
  <b>lag(sum(amount), 1)
   over (order by yearweek(payment_date)) prev_wk_tot,</b> 
  <b>lead(sum(amount), 1)
   over (order by yearweek(payment_date)) next_wk_tot,</b>
FROM payment
GROUP BY yearweek(payment_date)
ORDER BY 1;
</pre>

<pre>
SELECT yearweek(payment_date) payment_week, 
 sum(amount) week_total, 
  <b>round((sum(amount) - lag(sum(amount), 1) 
  over (order by yearweek(payment_date))) / lag(sum(amount), 1)
    over (order by yearweek(payment_date)) * 100, 1) pct_diff</b> 
FROM payment
GROUP BY yearweek(payment_date)
ORDER BY 1;
</pre>

## Column Value Concatenation

<pre>
SELECT f.title, 
 <B>group_concat(a.last_name order by a.last_name separator ', ') actors</b>
FROM actor a 
INNER JOIN film_actor fa 
 ON a.actor_id = fa.actor_id 
INNER JOIN film f 
 ON fa.film_id = f.film_id 
GROUP BY f.title
HAVING count(*) = 3;
</pre>