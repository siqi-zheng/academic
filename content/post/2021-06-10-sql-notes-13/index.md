---
title: 'Learning SQL Notes #13: Metadata'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The thirteenth note from Learning SQL"

date: "2021-06-10T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Data About Data](#data-about-data)
- [information_schema](#information_schema)
- [Working with Metadata](#working-with-metadata)
  - [Schema Generation Scripts](#schema-generation-scripts)
  - [Deployment Verification](#deployment-verification)
  - [Dynamic SQL Generation](#dynamic-sql-generation)


A database server also needs to store information about all of the database objects (tables, views, indexes, etc.) that were created to store this data in a database. This chapter discusses how and where this information, known as *metadata*, is stored, how you can access it, and how you can use it to build flexible systems.

# Data About Data

Metadata is essentially data about data. Every time you create a database object, the database server needs to record various pieces of information. For example, if you were to create a table with multiple columns, a primary key constraint, three indexes, and a foreign key constraint, the database server would need to store all the following information:

* Table name 
* Table storage information (tablespace, initial size, etc.) 
* Storage engine 
* Column names 
* Column data types 
* Default column values 
* not null column constraints 
* Primary key columns 
* Primary key name
* Name of primary key index
* Index names 
* Index types (B-tree, bitmap) 
* Indexed columns 
* Index column sort order (ascending or descending) 
* Index storage information 
* Foreign key name 
* Foreign key columns
* Associated table/columns for foreign keys

This data is collectively known as the *data dictionary* or *system catalog*. The database server needs to store this data persistently, and it needs to be able to quickly retrieve this data in order to verify and execute SQL statements. Additionally, the database server must safeguard this data so that it can be modified only via an appropriate mechanism, such as the `alter` table statement.

Every database server uses a different mechanism to publish metadata, such as:
* A set of views, such as Oracle Database’s user_tables and all_constraints views
* A set of system-stored procedures, such as SQL Server’s sp_tables procedure or Oracle Database’s dbms_metadata package
* A special database, such as MySQL’s information_schema database

# information_schema

All of the objects available within the information_schema database (or *schema*, in the case of SQL Server) are views. Unlike the describe utility, the views within information_schema can be queried and, thus, used programmatically.

<table frame="box" rules="all" summary="A reference that lists all INFORMATION_SCHEMA tables."><col style="width: 22%"><col style="width: 55%"><col style="width: 11%"><col style="width: 11%"><thead><tr><th>Table Name</th>
<th>Description</th>
<th>Introduced</th>
<th>Deprecated</th>
</tr></thead><tbody><tr><th scope="row"><code class="literal">ADMINISTRABLE_ROLE_AUTHORIZATIONS</code></a></th>
<td>Grantable users or roles for current user or role</td>
<td>8.0.19</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">APPLICABLE_ROLES</code></a></th>
<td>Applicable roles for current user</td>
<td>8.0.19</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">CHARACTER_SETS</code></a></th>
<td>Available character sets</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">CHECK_CONSTRAINTS</code></a></th>
<td>Table and column CHECK constraints</td>
<td>8.0.16</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">COLLATION_CHARACTER_SET_APPLICABILITY</code></a></th>
<td>Character set applicable to each collation</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">COLLATIONS</code></a></th>
<td>Collations for each character set</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">COLUMN_PRIVILEGES</code></a></th>
<td>Privileges defined on columns</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">COLUMN_STATISTICS</code></a></th>
<td>Histogram statistics for column values</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">COLUMNS</code></a></th>
<td>Columns in each table</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">COLUMNS_EXTENSIONS</code></a></th>
<td>Column attributes for primary and secondary storage engines</td>
<td>8.0.21</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS</code></a></th>
<td>Current number of consecutive failed connection attempts per account</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ENABLED_ROLES</code></a></th>
<td>Roles enabled within current session</td>
<td>8.0.19</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ENGINES</code></a></th>
<td>Storage engine properties</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">EVENTS</code></a></th>
<td>Event Manager events</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">FILES</code></a></th>
<td>Files that store tablespace data</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_BUFFER_PAGE</code></a></th>
<td>Pages in InnoDB buffer pool</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_BUFFER_PAGE_LRU</code></a></th>
<td>LRU ordering of pages in InnoDB buffer pool</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_BUFFER_POOL_STATS</code></a></th>
<td>InnoDB buffer pool statistics</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CACHED_INDEXES</code></a></th>
<td>Number of index pages cached per index in InnoDB buffer pool</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CMP</code></a></th>
<td>Status for operations related to compressed InnoDB tables</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CMP_PER_INDEX</code></a></th>
<td>Status for operations related to compressed InnoDB tables and indexes</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CMP_PER_INDEX_RESET</code></a></th>
<td>Status for operations related to compressed InnoDB tables and indexes</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CMP_RESET</code></a></th>
<td>Status for operations related to compressed InnoDB tables</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CMPMEM</code></a></th>
<td>Status for compressed pages within InnoDB buffer pool</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_CMPMEM_RESET</code></a></th>
<td>Status for compressed pages within InnoDB buffer pool</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_COLUMNS</code></a></th>
<td>Columns in each InnoDB table</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_DATAFILES</code></a></th>
<td>Data file path information for InnoDB file-per-table and general tablespaces</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FIELDS</code></a></th>
<td>Key columns of InnoDB indexes</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FOREIGN</code></a></th>
<td>InnoDB foreign-key metadata</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FOREIGN_COLS</code></a></th>
<td>InnoDB foreign-key column status information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FT_BEING_DELETED</code></a></th>
<td>Snapshot of INNODB_FT_DELETED table</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FT_CONFIG</code></a></th>
<td>Metadata for InnoDB table FULLTEXT index and associated processing</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FT_DEFAULT_STOPWORD</code></a></th>
<td>Default list of stopwords for InnoDB FULLTEXT indexes</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FT_DELETED</code></a></th>
<td>Rows deleted from InnoDB table FULLTEXT index</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FT_INDEX_CACHE</code></a></th>
<td>Token information for newly inserted rows in InnoDB FULLTEXT index</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_FT_INDEX_TABLE</code></a></th>
<td>Inverted index information for processing text searches against InnoDB table FULLTEXT index</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_INDEXES</code></a></th>
<td>InnoDB index metadata</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_METRICS</code></a></th>
<td>InnoDB performance information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_SESSION_TEMP_TABLESPACES</code></a></th>
<td>Session temporary-tablespace metadata</td>
<td>8.0.13</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_TABLES</code></a></th>
<td>InnoDB table metadata</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_TABLESPACES</code></a></th>
<td>InnoDB file-per-table, general, and undo tablespace metadata</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_TABLESPACES_BRIEF</code></a></th>
<td>Brief file-per-table, general, undo, and system tablespace metadata</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_TABLESTATS</code></a></th>
<td>InnoDB table low-level status information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_TEMP_TABLE_INFO</code></a></th>
<td>Information about active user-created InnoDB temporary tables</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_TRX</code></a></th>
<td>Active InnoDB transaction information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">INNODB_VIRTUAL</code></a></th>
<td>InnoDB virtual generated column metadata</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">KEY_COLUMN_USAGE</code></a></th>
<td>Which key columns have constraints</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">KEYWORDS</code></a></th>
<td>MySQL keywords</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">MYSQL_FIREWALL_USERS</code></a></th>
<td>Firewall in-memory data for account profiles</td>
<td></td>
<td>8.0.26</td>
</tr><tr><th scope="row"><code class="literal">MYSQL_FIREWALL_WHITELIST</code></a></th>
<td>Firewall in-memory data for account profile allowlists</td>
<td></td>
<td>8.0.26</td>
</tr><tr><th scope="row"><code class="literal">ndb_transid_mysql_connection_map</code></a></th>
<td>NDB transaction information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">OPTIMIZER_TRACE</code></a></th>
<td>Information produced by optimizer trace activity</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">PARAMETERS</code></a></th>
<td>Stored routine parameters and stored function return values</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">PARTITIONS</code></a></th>
<td>Table partition information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">PLUGINS</code></a></th>
<td>Plugin information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">PROCESSLIST</code></a></th>
<td>Information about currently executing threads</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">PROFILING</code></a></th>
<td>Statement profiling information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">REFERENTIAL_CONSTRAINTS</code></a></th>
<td>Foreign key information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">RESOURCE_GROUPS</code></a></th>
<td>Resource group information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ROLE_COLUMN_GRANTS</code></a></th>
<td>Column privileges for roles available to or granted by currently enabled roles</td>
<td>8.0.19</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ROLE_ROUTINE_GRANTS</code></a></th>
<td>Routine privileges for roles available to or granted by currently enabled roles</td>
<td>8.0.19</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ROLE_TABLE_GRANTS</code></a></th>
<td>Table privileges for roles available to or granted by currently enabled roles</td>
<td>8.0.19</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ROUTINES</code></a></th>
<td>Stored routine information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">SCHEMA_PRIVILEGES</code></a></th>
<td>Privileges defined on schemas</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">SCHEMATA</code></a></th>
<td>Schema information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">SCHEMATA_EXTENSIONS</code></a></th>
<td>Schema options</td>
<td>8.0.22</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ST_GEOMETRY_COLUMNS</code></a></th>
<td>Columns in each table that store spatial data</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ST_SPATIAL_REFERENCE_SYSTEMS</code></a></th>
<td>Available spatial reference systems</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">ST_UNITS_OF_MEASURE</code></a></th>
<td>Acceptable units for ST_Distance()</td>
<td>8.0.14</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">STATISTICS</code></a></th>
<td>Table index statistics</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLE_CONSTRAINTS</code></a></th>
<td>Which tables have constraints</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLE_CONSTRAINTS_EXTENSIONS</code></a></th>
<td>Table constraint attributes for primary and secondary storage engines</td>
<td>8.0.21</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLE_PRIVILEGES</code></a></th>
<td>Privileges defined on tables</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLES</code></a></th>
<td>Table information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLES_EXTENSIONS</code></a></th>
<td>Table attributes for primary and secondary storage engines</td>
<td>8.0.21</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLESPACES</code></a></th>
<td>Tablespace information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TABLESPACES_EXTENSIONS</code></a></th>
<td>Tablespace attributes for primary storage engines</td>
<td>8.0.21</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TP_THREAD_GROUP_STATE</code></a></th>
<td>Thread pool thread group states</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TP_THREAD_GROUP_STATS</code></a></th>
<td>Thread pool thread group statistics</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TP_THREAD_STATE</code></a></th>
<td>Thread pool thread information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">TRIGGERS</code></a></th>
<td>Trigger information</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">USER_ATTRIBUTES</code></a></th>
<td>User comments and attributes</td>
<td>8.0.21</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">USER_PRIVILEGES</code></a></th>
<td>Privileges defined globally per user</td>
<td></td>
<td></td>
</tr><tr><th scope="row"><code class="literal">VIEW_ROUTINE_USAGE</code></a></th>
<td>Stored functions used in views</td>
<td>8.0.13</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">VIEW_TABLE_USAGE</code></a></th>
<td>Tables and views used in views</td>
<td>8.0.13</td>
<td></td>
</tr><tr><th scope="row"><code class="literal">VIEWS</code></a></th>
<td>View information</td>
<td></td>
<td></td>
</tr></tbody></table>

# Working with Metadata

## Schema Generation Scripts

Generate a script that will create the various tables, indexes, views, and so on, that the team has deployed. Build a script that will create the sakila.category table. The following codes can be used to create a template-like SQL script.

```sql
SELECT 'CREATE TABLE category (' create_table_statement 
UNION ALL
SELECT cols.txt 
FROM 
(SELECT concat(' ',column_name, ' ', column_type, 
CASE
WHEN is_nullable = 'NO' THEN ' not null' ELSE ''
END, CASE
WHEN extra IS NOT NULL AND extra LIKE 'DEFAULT_GENERATED%' THEN concat(' DEFAULT ',column_default,substr(extra,18)) WHEN extra IS NOT NULL THEN concat(' ', extra)
ELSE '' END, ',') txt
FROM information_schema.columns 
WHERE table_schema = 'sakila' AND table_name = 'category' 
ORDER BY ordinal_position 
) cols 
UNION ALL
SELECT concat(' constraint primary key (') 
FROM information_schema.table_constraints 
WHERE table_schema = 'sakila' AND table_name = 'category' 
AND constraint_type = 'PRIMARY KEY' 
UNION ALL
SELECT cols.txt 
FROM 
(SELECT concat(CASE WHEN ordinal_position > 1 THEN ' ,' 
ELSE ' ' END, column_name) txt
FROM information_schema.key_column_usage 
WHERE table_schema = 'sakila' AND table_name = 'category' 
AND constraint_name = 'PRIMARY'
ORDER BY ordinal_position 
) cols 
UNION ALL 
SELECT ' )' 
UNION ALL
SELECT ')';
```

## Deployment Verification

After the deployment scripts have been run, it’s a good idea to run a verification script to ensure that the new schema objects are in place with the appropriate columns, indexes, primary keys, and so forth. Here’s a query that returns the number of columns, number of indexes, and number of primary key constraints (0 or 1) for each table in the Sakila schema:

```sql
SELECT tbl.table_name, 
 (SELECT count(*) 
 FROM information_schema.columns clm
 WHERE clm.table_schema = tbl.table_schema
 AND clm.table_name = tbl.table_name) num_columns, 
 (SELECT count(*) 
 FROM information_schema.statistics sta 
 WHERE sta.table_schema = tbl.table_schema 
 AND sta.table_name = tbl.table_name) num_indexes,
 (SELECT count(*) 
 FROM information_schema.table_constraints tc 
 WHERE tc.table_schema = tbl.table_schema 
 AND tc.table_name = tbl.table_name
 AND tc.constraint_type = 'PRIMARY KEY') num_primary_keys 
 FROM information_schema.tables tbl
 WHERE tbl.table_schema = 'sakila' AND tbl.table_type = 'BASE TABLE'
 ORDER BY 1;
```

| TABLE_NAME | num_columns | num_indexes | num_primary_keys |
| :--------- | ----------- | ----------- | ---------------: |
| actor      | 4           | 2           |                1 |

## Dynamic SQL Generation

Most relational database servers, including SQL Server, Oracle Database, and MySQL, allow SQL statements to be submitted to the server as strings. Submit‐ ting strings to a database engine rather than utilizing its SQL interface is generally known as *dynamic SQL execution*.

*Oracle’s PL/SQL language*

`execute immediate`

*SQL Server*

`sp_executesql`

*MySQL*

`prepare, execute, deallocate`

```sql
SET @qry = 'SELECT customer_id, first_name, last_name FROM customer'; 
PREPARE dynsql1 FROM @qry; 
EXECUTE dynsql1;
DEALLOCATE PREPARE dynsql1;

/*conditions can be specified at runtime*/
SET @qry = 'SELECT customer_id, first_name, last_name FROM customer WHERE customer_id = ?';
PREPARE dynsql2 FROM @qry;
SET @custid = 9;
EXECUTE dynsql2 USING @custid;
SET @custid = 145;
EXECUTE dynsql2 USING @custid;
DEALLOCATE PREPARE dynsql2;
```

Or you can do the following:

<pre>
SELECT concat('SELECT ', concat_ws(',', cols.col1, cols.col2),
' FROM customer WHERE customer_id = ?') 
<b>INTO @qry </b>
FROM (SELECT
max(CASE WHEN ordinal_position = 1 THEN column_name 
ELSE NULL END) col1,
max(CASE WHEN ordinal_position = 2 THEN column_name
ELSE NULL END) col2
FROM information_schema.columns 
WHERE table_schema = 'sakila' AND table_name = 'customer' 
GROUP BY table_name
) cols;
</pre>

```sql
PREPARE dynsql3 FROM @qry; 
SET @custid = 45; Query OK, 0 rows affected (0.00 sec)
EXECUTE dynsql3 USING @custid;
DEALLOCATE PREPARE dynsql3;
```

Note: Generally, it would be better to generate the query using a procedural language that includes looping constructs, such as Java, PL/SQL, Transact-SQL, or MySQL’s Stored Procedure Language.