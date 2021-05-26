---
title: 'Learning SQL Notes #1'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The first note from Learning SQL"

date: "2021-05-28T18:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Introduction to Databases](#introduction-to-databases)
  - [进入数据库](#进入数据库)
  - [找到那张表](#找到那张表)
  - [创建表格](#创建表格)
  - [表格添加行](#表格添加行)
  - [表格修改某个cell](#表格修改某个cell)
  - [表格删除某一行](#表格删除某一行)
  - [查看一张表格](#查看一张表格)
  - [查看所有表格](#查看所有表格)
  - [丢弃一张表格](#丢弃一张表格)
  - [导出XML](#导出xml)
- [Table Creation (CH. 2)](#table-creation-ch-2)
  - [1   Design](#1---design)
  - [2   Refinement](#2---refinement)
  - [3   Building SQL Schema Statements](#3---building-sql-schema-statements)
  - [BOUNUS: NULL?](#bounus-null)

## Introduction to Databases

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
5. Two-column primary key is also possible depending on the context (CH.2)；
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


### 进入数据库

To see the see the `mysql>` prompt:
```
mysql -u root -p;
```
Then type `show databases;`; 显示所有数据表

### 找到那张表

To select a table, type `use table_name;`; 

Can do the following:
```
mysql -u root -p table_name;
```
在**R**中，可以在global environment里面看到。

### 创建表格
```SQL
CREATE TABLE table_name /*创建表格：……*/
    (col_0 smallint;
    col_1 VARCHAR(30);
    col_2 timestamp;
    CONSTRAINT pk_col_0 PRIMARY KEY (col_0) /*将col_0设为primary key*/
    ); /*The most basic method to create a database*/
```
类比**R**的代码：
```R
df <- data.frame()
# x1 = c(7, 3, 2, 9, 0),
# x2 = c(4, 4, 1, 1, 8),
# x0 = c(5, 3, 9, 2, 4)
# Primary key可以自行添加
```

### 表格添加行
```SQL
INSERT INTO table_name (col_0, col_1, col_2) /*插入*/
VALUES (27, 'Rdm Name', 'Acme Paper Corporation'); /*什么值*/
/*The most basic method to insert a full row into a database*/
```
`Query OK, 1 row affected`$\Rightarrow$one row was added to the database

类比**R**的代码：
```r
new_row <- c(27, 'Rdm Name', 'Acme Paper Corporation')
rbind(df, new_row)
```

* You are not required to provide data for every column in the table unless the column cannot be NULL;
* MySQL will convert the **string** to a **date** for you as long as the **format is followed**; 
  ```
  ERROR 1292 (22007): Incorrect date value: 'DEC-21-1980' for column 'birth_date' at row 1
  ```



### 表格修改某个cell
```SQL
UPDATE table_name 
/*规定列*/  /*要输入的值*/
SET name = 'Certificate of Deposit'
WHERE col_2 = 'CD'; /*规定行,否则整列都被换掉*/
```
类比**R**的代码：
```r
df[df$col_2=='CD', "name"] <- 'Certificate of Deposit'
# 规定行，规定列
```

### 表格删除某一行

```SQL
DELETE ...
/*规定列*/  /*要输入的值*/
FROM table_name
WHERE col_2 = 'CD'; /*规定行,否则整列都被删掉*/
```
类比**R**的代码：
```r
df[df$col_2=='CD', ] <- NULL
```

### 查看一张表格
```SQL
DESC favorite_food;
```
类比**R**的代码：
```r
str(df)
summary(df)
glimpse(df)
```

Describe the table.

### 查看所有表格
```SQL
show tables
```

### 丢弃一张表格
```SQL
drop table xxx
```


### 导出XML

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
1. Compound objects要分开。比如姓和名，地址等；
2. 如果一列中有数量不等的独立个体，则再开一张表；
3. 是否有guarantee uniqueness的primary key column？


### 3   Building SQL Schema Statements
见创建表格。

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

After processing the create table statement, the MySQL server returns the message “Query OK, 0 rows affected,” which tells me that the statement had no **syntax errors**.

### BOUNUS: NULL?

Null is used for various cases where a value cannot be supplied, such as: 
• Not applicable 
• Unknown
• Empty set
