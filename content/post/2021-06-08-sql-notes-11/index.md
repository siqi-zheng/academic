---
title: 'Learning SQL Notes #11: Indexes and Constraints'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The eleventh note from Learning SQL"

date: "2021-06-08T05:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Indexes](#indexes)
  - [Index Creation](#index-creation)
    - [Unique indexes](#unique-indexes)
    - [Multicolumn indexes](#multicolumn-indexes)
  - [Types of Indexes](#types-of-indexes)
    - [B-tree indexes](#b-tree-indexes)
    - [Bitmap indexes](#bitmap-indexes)
    - [Text indexes](#text-indexes)
  - [How Indexes Are Used](#how-indexes-are-used)
  - [The Downside of Indexes](#the-downside-of-indexes)
- [Constraints](#constraints)
    - [Constraint Creation](#constraint-creation)


# Indexes

The server simply places the data in the next available location within the file (the server
maintains a list of free space for each table).

To find all customers whose last name begins with Y, the server must visit each row in the customer table and inspect the contents of the last_name column; if the last name begins with Y, then the row is added to the result set. This type of access is known as a *table* *scan*.

An index is simply a mechanism for finding a specific item within a resource. A database server uses indexes to locate rows in a table. Indexes are special tables that, unlike normal data tables, *are* kept in a specific order. Instead of containing *all* of the data about an entity, however, an index contains only the column (or columns) used to locate rows in the data table, along with information describing where the rows are physically located. Therefore, the role of indexes is to facilitate the retrieval of a subset of a table’s rows and columns *without* the need to inspect every row in the table.

## Index Creation

```sql
/*MySQL*/
ALTER TABLE customer 
ADD INDEX idx_email (email);
/*OR*/
ALTER TABLE customer 
DROP INDEX idx_email;

/*SQL Server*/
CREATE INDEX idx_email 
ON customer (email);

SHOW INDEX FROM customer \G;
```

To create indexes, we can

<pre>
CREATE TABLE customer ( 
  customer_id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
  ...
  <b>PRIMARY KEY (customer_id),
  KEY idx_fk_store_id (store_id), 
  KEY idx_fk_address_id (address_id), 
  KEY idx_last_name (last_name),</b>
  ...
</pre>  

### Unique indexes

```sql
/*MySQL*/
ALTER TABLE customer 
ADD UNIQUE INDEX idx_email (email);

/*SQL Server/Oracle Database*/
CREATE UNIQUE INDEX idx_email 
ON customer (email);
```

You should not build unique indexes on your primary key column(s), since the server already checks uniqueness for primary key values.

### Multicolumn indexes

```sql
/*MySQL*/
ALTER TABLE customer 
ADD INDEX idx_full_name (last_name, first_name);

/*SQL Server/Oracle Database*/
CREATE UNIQUE INDEX idx_email 
ON customer (email);
```


## Types of Indexes

### B-tree indexes

All the indexes shown thus far are *balanced-tree indexes*, which are more commonly known as *B-tree indexes*. MySQL, Oracle Database, and SQL Server all default to B-tree indexing.

* B-tree indexes are organized as trees, with one or more levels of branch nodes leading to a single level of leaf nodes. 
* The server would look at the top branch node (called the root node) and follow the link to the branch node.
* The server can add or remove branch nodes to redistribute the values more evenly and can even add or remove an entire level of branch nodes. 

### Bitmap indexes

If there are only two different values (stored as 1 for active and 0 for inactive) and far more active customers, it can be difficult to maintain a balanced B-tree index as the number of customers grows.

For columns that contain only a small number of values across a large number of rows (known as *low-cardinality data*), Oracle Database includes bitmap indexes, which generate a bitmap for each value stored in the column.

```sql
/*Oracle Database*/
CREATE BITMAP INDEX idx_active ON customer (active);
```

Bitmap indexes are commonly used in data warehousing environments, where large amounts of data are generally indexed on columns containing relatively few values (e.g., sales quarters, geographic regions, products, salespeople).

### Text indexes

## How Indexes Are Used

```sql
/*MySQL*/
EXPLAIN
SELECT customer_id, first_name, last_name 
FROM customer
WHERE first_name LIKE 'S%' AND last_name LIKE 'P%';

/*SQL Server*/
set show plan_text

/*Oracle Database*/
explain plan
```

For this query, the server can employ any of the following strategies: 

* Scan all rows in the customer table.

* Use the index on the last_name column to find all customers whose last name starts with P; then visit each row of the customer table to find only rows whose first name starts with S.

* Use the index on the last_name and first_name columns to find all customers whose last name starts with P and whose first name starts with S.

Looking at the query results, the `possible_keys` column tells you that the server could decide to use either the `idx_last_name` or the `idx_full_name` index, and the key column tells you that the `idx_full_name` index was chosen. Furthermore, the `type` column tells you that a range scan will be utilized, meaning that the database server will be looking for a range of values in the index, rather than expecting to retrieve a single row.


## The Downside of Indexes

**Every index is a table** (a special type of table but still a table). Therefore, every time a row is added to or removed from a table, all indexes on that table must be modified. When a row is updated, any indexes on the column or columns that were affected need to be modified as well. Therefore, the more indexes you have, the more work the server needs to do to keep all schema objects up-to-date, which tends to slow things down.

Indexes also require **disk space** as well as some amount of care from your administrators, so the best strategy is to add an index when a clear need arises. If you need an index for only special purposes, such as a monthly maintenance routine, you can always add the index, run the routine, and then drop the index until you need it again. In the case of data warehouses, where indexes are crucial during business hours as users run reports and ad hoc queries but are problematic when data is being loaded into the warehouse overnight, it is a common practice to drop the indexes before data is loaded and then re-create them before the warehouse opens for business.

In general, you should strive to have neither too many indexes nor too few. If you aren’t sure how many indexes you should have, you can use this strategy as a default:

* Make sure all primary key columns are indexed (most servers automatically cre‐ ate unique indexes when you create primary key constraints). For multicolumn primary keys, consider building additional indexes on a subset of the primary key columns or on all the primary key columns but in a different order than the primary key constraint definition.

* Build indexes on all columns that are referenced in foreign key constraints. Keep in mind that the server checks to make sure there are no child rows when a par‐ ent is deleted, so it must issue a query to search for a particular value in the col‐ umn. If there’s no index on the column, the entire table must be scanned.

* Index any columns that will frequently be used to retrieve data. Most date columns are good candidates, along with short (2- to 50-character) string columns.

# Constraints

A constraint is simply a restriction placed on one or more columns of a table. There are several different types of constraints, including:

*Primary key constraints*
Identify the column or columns that guarantee uniqueness within a table

*Foreign key constraints*
Restrict one or more columns to contain only values found in another table’s pri‐ mary key columns (may also restrict the allowable values in other tables if update cascade or delete cascade rules are established)

*Unique constraints* 
Restrict one or more columns to contain unique values within a table (primary key constraints are a special type of unique constraint)

*Check constraints*
Restrict the allowable values for a column

If the server allows you to change a customer’s ID in the customer table without changing the same customer ID in the rental table, then you will end up with rental data that no longer points to valid customer records (known as *orphaned rows*). With primary and foreign key constraints in place, however, the server will either raise an error if an attempt is made to modify or delete data that is referenced by other tables or propagate the changes to other tables for you

Note: If you want to use foreign key constraints with the MySQL server, you must use the *InnoDB* storage engine for your tables.

### Constraint Creation

<pre>
CREATE TABLE customer (
  ...
  <b>PRIMARY KEY (customer_id), </b>
  KEY idx_fk_store_id (store_id), 
  KEY idx_fk_address_id (address_id), 
  KEY idx_last_name (last_name), 
  <b>CONSTRAINT fk_customer_address FOREIGN KEY (address_id) REFERENCES address (address_id) ON DELETE RESTRICT ON UPDATE CASCADE, 
  CONSTRAINT fk_customer_store FOREIGN KEY (store_id)REFERENCES store (store_id) ON DELETE RESTRICT ON UPDATE CASCADE</b>
)ENGINE=InnoDB DEFAULT CHARSET=utf8; 

/*For existing tables, you can do"*/
ALTER TABLE customer 
<b>ADD CONSTRAINT</b> fk_customer_address FOREIGN KEY (address_id) 
REFERENCES address (address_id) ON DELETE RESTRICT ON UPDATE CASCADE;

ALTER TABLE customer
<b>ADD CONSTRAINT</b> fk_customer_store FOREIGN KEY (store_id)
REFERENCES store (store_id) ON DELETE RESTRICT ON UPDATE CASCADE;

/*if you want to drop them*/
ALTER TABLE customer 
<b>DROP CONSTRAINT</b> fk_customer_address;

ALTER TABLE customer
<b>DROP CONSTRAINT</b> fk_customer_store F;
</pre>

* on delete restrict, which will cause the server to raise an error if a row is deleted in the parent table (address or store) that is referenced in the child table (customer)
* on update cascade, which will cause the server to propagate a change to the primary key value of a parent table (address or store) to the child table (customer)



<table><thead>
<tr>
<th>Parameter</th>
<th>Description</th>
</tr>
</thead><tbody>
<tr>
<td><code>ON DELETE NO ACTION</code></td>
<td><em>Default action.</em> If there are any existing references to the key being deleted, the transaction will fail at the end of the statement. The key can be updated, depending on the <code>ON UPDATE</code> action. <br><br>Alias: <code>ON DELETE RESTRICT</code></td>
</tr>
<tr>
<td><code>ON UPDATE NO ACTION</code></td>
<td><em>Default action.</em> If there are any existing references to the key being updated, the transaction will fail at the end of the statement. The key can be deleted, depending on the <code>ON DELETE</code> action. <br><br>Alias: <code>ON UPDATE RESTRICT</code></td>
</tr>
<tr>
<td><code>ON DELETE RESTRICT</code> / <code>ON UPDATE RESTRICT</code></td>
<td><code>RESTRICT</code> and <code>NO ACTION</code> are currently equivalent until options for deferring constraint checking are added. To set an existing foreign key action to <code>RESTRICT</code>, the foreign key constraint must be dropped and recreated.</td>
</tr>
<tr>
<td><code>ON DELETE CASCADE</code> / <code>ON UPDATE CASCADE</code></td>
<td>When a referenced foreign key is deleted or updated, all rows referencing that key are deleted or updated, respectively. If there are other alterations to the row, such as a <code>SET NULL</code> or <code>SET DEFAULT</code>, the delete will take precedence. <br><br>Note that <code>CASCADE</code> does not list objects it drops or updates, so it should be used cautiously.</td>
</tr>
<tr>
<td><code>ON DELETE SET NULL</code> / <code>ON UPDATE SET NULL</code></td>
<td>When a referenced foreign key is deleted or updated, respectively, the columns of all rows referencing that key will be set to <code>NULL</code>. The column must allow <code>NULL</code> or this update will fail.</td>
</tr>
<tr>
<td><code>ON DELETE SET DEFAULT</code> / <code>ON UPDATE SET DEFAULT</code></td>
<td>When a referenced foreign key is deleted or updated, the columns of all rows referencing that key are set to the default value for that column. <br/><br/> If the default value for the column is null, or if no default value is provided and the column does not have a <a href='/docs/v21.1/not-null'><code>NOT NULL</code></a> constraint, this will have the same effect as <code>ON DELETE SET NULL</code> or <code>ON UPDATE SET NULL</code>. The default value must still conform with all other constraints, such as <code>UNIQUE</code>.</td>
</tr>
</tbody></table>