---
title: 'Learning SQL Notes #1'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The first note from Learning SQL"

date: "2021-05-25T18:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Introduction to Databases](#introduction-to-databases)
  - [More about Relational Databases](#more-about-relational-databases)
  - [Find Databases](#find-databases)
  - [Find a Table](#find-a-table)
  - [Create a Table](#create-a-table)
  - [Add a Row](#add-a-row)
  - [Change a Cell](#change-a-cell)
  - [Delete a Row](#delete-a-row)
  - [Table Overview](#table-overview)
  - [Show Tables](#show-tables)
  - [Drop a Table](#drop-a-table)
  - [Export to XML](#export-to-xml)
- [Table Creation (CH. 2)](#table-creation-ch-2)
  - [1   Design](#1---design)
  - [2   Refinement](#2---refinement)
  - [3   Building SQL Schema Statements](#3---building-sql-schema-statements)

## Introduction to Databases

* SQL was initially created to be the language for generating, manipulating, and retrieving data from relational databases.
* A database is a set of related information. 
* *Database systems* are computerized data storage and retrieval mechanisms.
* *Nonrelational Database Systems*: 
  * In a *hierarchical* database system, for example, data is represented as one or more tree structures. The hierarchical database system provides tools for locating a particular customer’s tree and then traversing the tree to find the desired accounts and/or transactions. Each node in the tree may have either zero or one parent and zero, one, or many children. 
  * *Network database system* exposes sets of records and sets of links that define relationships between different records.
* Data can  be represented as sets of *tables*. Rather than using pointers to navigate between related entities, redundant data is used to link records in different tables: *relational model*.


### More about Relational Databases

1. Now columns/rows are constrained due to *physical limit* or *maintainability*;
2. *Primary key* includes information that **uniquely identifies** a row in that table;
   1. If more than one column, then *compound key*; 
   2. If select, say, first name, then it is a *natural key*;
   3. If select an id, then it is a *surrogate key*;
   4. **NEVER be allowed to change!**
   5. Possible error:
      ```
      ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
      ```    
3. More than one identifiers in a table including the *primary key*: *foreign keys*, connect the entities in different tables;
4. Make sure that there is only **one place** in the database that holds, say, the customer’s name; otherwise, the data might be changed in one place but not another, causing the data in the database to be unreliable. The process of refining a database design to ensure that each independent piece of information is in only **one place** (except for foreign keys) is known as *normalization*. (Think about the concept of *Tidy Data* in **R**!)
5. Two-column primary key is also possible depending on the context (CH.2); 
6. Foreign key constraint limits the id to those exist in another table (CH.2); Possible error:
   ```
   ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails ('sakila'.'favorite_food', CONSTRAINT 'fk_fav_food_person_id' FOREIGN KEY
   ('person_id') REFERENCES 'person' ('person_id'))
   ```
7. Ways to generate primary keys:
   * Look at the largest value currently in the table and add one. 
   * Let the database server provide the value for you.
   ```SQL
   ALTER TABLE table_name MODIFY col_0 SMALLINT UNSIGNED AUTO_INCREMENT; 

   set foreign_key_checks=0; /*IMPORTANT*/
   ALTER TABLE person
       MODIFY person_id SMALLINT UNSIGNED AUTO_INCREMENT;
   set foreign_key_checks=1; /*IMPORTANT*/
   ```


### Find Databases

To see the see the `mysql>` prompt:
```
mysql -u root -p;
```
Then type `show databases;` to display all databases;

### Find a Table

To select a table, type `use table_name;`; 

Can do the following:
```
mysql -u root -p table_name;
```
In**R**, one can find it under the global environment.

### Create a Table
```SQL
CREATE TABLE table_name /*Create a table with name: ……*/
    (col_0 smallint;
    col_1 VARCHAR(30);
    col_2 timestamp;
    CONSTRAINT pk_col_0 PRIMARY KEY (col_0) /*set col_0 as primary key*/
    ); /*The most basic method to create a database*/
```
**R** codes: 
```R
df <- data.frame()
# x1 = c(7, 3, 2, 9, 0),
# x2 = c(4, 4, 1, 1, 8),
# x0 = c(5, 3, 9, 2, 4)
# Primary key can only be added manually
```

### Add a Row
```SQL
INSERT INTO table_name (col_0, col_1, col_2) /*The table*/
VALUES (27, 'Rdm Name', 'Acme Paper Corporation'); /*The values*/
/*The most basic method to insert a full row into a database*/
```
`Query OK, 1 row affected`$\Rightarrow$one row was added to the database

**R** codes: 
```r
new_row <- c(27, 'Rdm Name', 'Acme Paper Corporation')
rbind(df, new_row)
```

* You are not required to provide data for every column in the table unless the column cannot be NULL;
* MySQL will convert the **string** to a **date** for you as long as the **format is followed**; 
  ```
  ERROR 1292 (22007): Incorrect date value: 'DEC-21-1980' for column 'birth_date' at row 1
  ```



### Change a Cell

```SQL
UPDATE table_name 
/*Fix column*/  /*Insert the values*/
SET name = 'Certificate of Deposit'
WHERE col_2 = 'CD'; /*Fix row, otherwise all will be replaced*/
```
**R** codes: 
```r
df[df$col_2=='CD', "name"] <- 'Certificate of Deposit'
# Fix column, fix row
```

### Delete a Row

```SQL
DELETE ...
/*Fix column*/
FROM table_name
WHERE col_2 = 'CD'; /*Fix row, otherwise all will be deleted*/
```
**R** codes: 
```r
df[df$col_2=='CD', ] <- NULL
```

### Table Overview
```SQL
DESC favorite_food;
```
**R** codes: 
```r
str(df)
summary(df)
glimpse(df)
```

Describe the table.

### Show Tables
```SQL
show tables
```

### Drop a Table
```SQL
drop table xxx
```


### Export to XML

Type the following in CMD:

```
mysql -u lrngsql -p --xml bank
```
**OR**

```SQL
SELECT * FROM table_name 
FOR XML AUTO, ELEMENTS /*IMPORTANT*/
```
No easy way to do so in **R**.




## Table Creation (CH. 2)

### 1   Design
What info is needed? Make a list.

### 2   Refinement
1. Compound objects need to be separated into multiple columns, including names or address;
2. If a column is a list containing zero, one, or more independent items, we need another table;
3. Need primary key column(s) to guarantee uniqueness.


### 3   Building SQL Schema Statements

Another type of constraint called a **check constraint** constrains the allowable values for a particular column. A check constraint to be attached to a **column definition**.

```SQL
eye_color CHAR(2) CHECK (eye_color IN ('BR','BL','GR'))
```
Possible error:
```
ERROR 1265 (01000): Data truncated for column 'eye_color' at row 1
```

MySQL does provide another character data type called `enum` that merges the check constraint into the data type definition.

```SQL
eye_color ENUM('BR','BL','GR')
```

**R** codes: 
```r
Enum <- function(...) {

  ## EDIT: use solution provided in comments to capture the arguments
  values <- sapply(match.call(expand.dots = TRUE)[-1L], deparse)

  stopifnot(identical(unique(values), values))

  res <- setNames(seq_along(values), values)
  res <- as.environment(as.list(res))
  lockEnvironment(res, bindings = TRUE)
  res
}

FRUITS <- Enum(APPLE, BANANA, MELON)
```

See https://stackoverflow.com/questions/33838392/enum-like-arguments-in-r for further details.

After processing the create table statement, the MySQL server returns the message "Query OK, 0 rows affected," which tells me that the statement had no **syntax errors**.

