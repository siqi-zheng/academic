---
title: 'Learning SQL Notes #3: Query Primer (CH. 3)'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The third note from Learning SQL"

date: "2021-05-26T20:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Query Mechanics](#query-mechanics)
- [Query Clauses](#query-clauses)
  - [SELECT](#select)
  - [FROM](#from)
    - [Table Links](#table-links)
    - [Table Aliases](#table-aliases)
  - [GROUP BY and HAVING (CH. 8)](#group-by-and-having-ch-8)
  - [ORDER BY](#order-by)
- [Filtering](#filtering)
  - [WHERE](#where)
    - [OR operator](#or-operator)
    - [AND operator](#and-operator)
    - [NOT operator](#not-operator)
    - [Expressions](#expressions)
    - [NULL](#null)

Complete sometime this summer:

- [ ] Finish Join Notes;
- [ ] Finish GROUP BY Notes;


## Query Mechanics

* Do you have permission to execute the statement? 
* Do you have permission to access the desired data?
* Is your statement syntax correct?

## Query Clauses

| Clause name |                                                                               Purpose |
| :---------- | ------------------------------------------------------------------------------------: |
| select      |                       Determines which columns to include in the   query’s result set |
| from        | Identifies the tables from which to retrieve data and how the tables should be joined |
| where       |                                                             Filters out unwanted data |
| group by    |                                   Used to group rows together by common column values |
| having      |                                                           Filters out unwanted groups |
| order by    |                               the rows of the final result set by one or more columns |

### SELECT

* Literals, such as numbers or strings 
* Expressions, such as transaction.amount * −1 
* Built-in function calls, such as ROUND(transaction.amount, 2)
* User-defined function calls

```SQL
SELECT version(), user(), database();
```

Results:

| version() |     user()     | database() |
| :-------- | :------------: | ---------: |
| 8.0.15    | root@localhost |     sakila |

```SQL
SELECT row1 AS r1;/*Column Aliases*/

SELECT DISTINCT row1 /*Removing Duplicates-should know beforehand whether duplicates are possible*/
```
**R** codes：
```r
unique()
```


### FROM

* Permanent tables (i.e., created using the create table statement) 
* Derived tables (i.e., rows returned by a subquery and held in memory) 
  ```sql
  SELECT *
  FROM 
  (SELECT first_name, last_name, email
  FROM customer
  WHERE first_name = 'JESSIE'
  ) AS cust;
  ```  
* Temporary tables (i.e., volatile data held in memory): any data inserted into a temporary table will disappear at some point
  ```sql
  CREATE TEMPORARY TABLE actors_j 
  (actor_id smallint(5), 
  first_name varchar(45), 
  last_name varchar(45)
  );
  ```
* Virtual tables (i.e., created using the create view statement): When you issue a query against a view, your query is **merged** with the view definition to create a final query to be executed.
  ```SQL
  CREATE VIEW cust_vw AS 
  SELECT customer_id, first_name, last_name, active
  FROM customer;
  ```
  
#### Table Links
See JOIN in the next note.

#### Table Aliases
```SQL
FROM customer AS c;
```

### GROUP BY and HAVING (CH. 8)
[] Haven't done

### ORDER BY

1. ```sql
   ORDER BY col1, col2, etc;
   ``` 
   **R** codes：
   ```r
   df[order(col1),]
   
   require(tidyverse)
   df %>%
   arrange(col1)
   ```

 2. ```sql
    ORDER BY col1;
    ORDER BY col1 desc;
    ```  
    **R** codes：
    ```r
    df[order(-col1),]
   
    require(tidyverse)
    df %>%
    arrange(desc(col1))
    ```

 3. ```sql
    SELECT col1, col2, col3;
    FROM table1
    ORDER BY 3; /*equivalent to ORDER BY col3*/
    ```    

    
## Filtering

### WHERE
```SQL
(...) AND (...)
(...) OR (...)
```
See **operators** and **expressions** for details.

#### OR operator
| Intermediate result  | Final result |
| :------------------- | -----------: |
| WHERE true OR true   |         true |
| WHERE true OR false  |         true |
| WHERE false OR true  |         true |
| WHERE false OR false |        false |

#### AND operator
| Intermediate result              | Final result |
| :------------------------------- | -----------: |
| WHERE (true OR true) AND true    |         true |
| WHERE (true OR false) AND true   |         true |
| WHERE (false OR true) AND true   |         true |
| WHERE (false OR false) AND true  |        false |
| WHERE (true OR true) AND false   |        false |
| WHERE (true OR false) AND false  |        false |
| WHERE (false OR true) AND false  |        false |
| WHERE (false OR false) AND false |        false |

#### NOT operator
| Intermediate result                  | Final result |
| :----------------------------------- | -----------: |
| WHERE NOT (true OR true) AND true    |        false |
| WHERE NOT (true OR false) AND true   |        false |
| WHERE NOT (false OR true) AND true   |        false |
| WHERE NOT (false OR false) AND true  |         true |
| WHERE NOT (true OR true) AND false   |        false |
| WHERE NOT (true OR false) AND false  |        false |
| WHERE NOT (false OR true) AND false  |        false |
| WHERE NOT (false OR false) AND false |        false |

#### Expressions
An expression can be any of the following:
* A number 
* A column in a table or view 
* A string literal, such as 'Maple Street' 
* A built-in function, such as concat('Learning', ' ', 'SQL') 
* A subquery
* A list of expressions, such as ('Boston', 'New York', 'Chicago')

Operators:
* Comparison operators, such as =, !=, <, <=, >, >=, <>, like, in, between, is null, exists 
* Arithmetic operators, such as +, −, *, /, DIV (integer division) and (% or MOD) for modulus

Note:
1. = can be used for date/string/number;
2. 'between and' can be used for date/string/number;
3. 'between and' is inclusive;
4. col1 (not) in ('A','B')/subqueries;
5. built-in function: left(name, 1) in ('A','B');
6. wildcards/regular expressions:
   * Strings beginning/ending with a certain **character** 
   * Strings beginning/ending with a **substring** 
   * Strings containing a certain **character** **anywhere** within the string 
   * Strings containing a **substring anywhere** within the string
   * Strings with a **specific format**, regardless of individual characters

| Wildcard character |                                Matches |
| :----------------- | -------------------------------------: |
| _                  |                  Exactly one character |
| %                  | Any number of characters (including 0) |

#### NULL

Null is used for various cases where a value cannot be supplied, such as: 

* Not applicable 
  Such as the employee ID column for a transaction that took place at an ATM machine
* Value not yet known 
  Such as when the federal ID is not known at the time a customer row is created 
* Value undefined
  Such as when an account is created for a product that has not yet been added to the database

Note:
* An expression can be null, but it can **never equal** null. IS NULL/IS NOT NULL.
* Two nulls are **never equal to each other**.