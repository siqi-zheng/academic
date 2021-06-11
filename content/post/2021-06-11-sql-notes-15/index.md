---
title: 'Learning SQL Notes #15: Working with Large Databases'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The fifteenth note from Learning SQL"

date: "2021-06-11T09:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Partitioning](#partitioning)
  - [Partitioning Concepts](#partitioning-concepts)
  - [Table Partitioning](#table-partitioning)
  - [Index Partitioning](#index-partitioning)
  - [Partitioning Methods](#partitioning-methods)
    - [Range partitioning](#range-partitioning)
    - [List partitioning](#list-partitioning)
    - [Hash partitioning](#hash-partitioning)
    - [Composite partitioning](#composite-partitioning)
  - [Partitioning Benefits](#partitioning-benefits)
- [Clustering](#clustering)
- [Sharding](#sharding)
- [Big Data](#big-data)
  - [Hadoop](#hadoop)
  - [NoSQL and Document Databases](#nosql-and-document-databases)
  - [Cloud Computing](#cloud-computing)
  - [Conclusion](#conclusion)

While relational databases face various challenges as data volumes continue to grow, there are strategies such as partitioning, clustering, and sharding that allow companies to continue to utilize relational databases by spreading data across multi‐ ple storage tiers and servers. Other companies have decided to move to big data platforms such as Hadoop in order to handle huge data volumes.

# Partitioning

The following tasks become more difficult and/or time consuming as a table grows past a few million rows:

* Query execution requiring full table scans 
* Index creation/rebuild 
* Data archival/deletion 
* Generation of table/index statistics 
* Table relocation (e.g., move to a different tablespace)
* Database backups

The best way to prevent administrative issues from occurring in the future is to break large tables into pieces, or *partitions*, when the table is first created (although tables can be partitioned later, it is easier to do so initially). Administrative tasks can be performed on individual partitions, often in parallel, and some tasks can skip one or more partitions entirely.

## Partitioning Concepts

While every partition must have the same schema definition (columns, column types, etc.), there are several administrative features that can differ for each partition:

* Partitions may be stored on different tablespaces, which can be on different physical storage tiers.
* Partitions can be compressed using different compression schemes. 
* Local indexes (more on this shortly) can be dropped for some partitions. 
* Table statistics can be frozen on some partitions, while being periodically refreshed on others.
* Individual partitions can be pinned into memory or stored in the database’s flash storage tier.

## Table Partitioning

The partitioning scheme available in most relational databases is *horizontal partitioning*, which assigns entire rows to exactly one partition. Tables may also be partitioned *vertically*, which involves assigning sets of columns to different partitions, but this must be done manually. When partitioning a table horizontally, you must choose a *partition key*, which is the column whose values are used to assign a row to a particular partition. In most cases, a table’s partition key consists of a single column, and a *partitioning function* is applied to this column to determine in which partition each row should reside.

## Index Partitioning 

If your partitioned table has indexes, you will get to choose whether a particular index should stay intact, known as a *global index*, or be broken into pieces such that each partition has its own index, which is called a *local index*. Global indexes span all partitions of the table and are useful for queries that do not specify a value for the partition key.

## Partitioning Methods

### Range partitioning

The most common usage is to break up tables by date ranges.

```sql
CREATE TABLE sales 
 (sale_id INT NOT NULL, 
 cust_id INT NOT NULL, 
 store_id INT NOT NULL, 
 sale_date DATE NOT NULL, 
 amount DECIMAL(9,2) 
 )
 PARTITION BY RANGE (yearweek(sale_date)) 
 (PARTITION s1 VALUES LESS THAN (202002), 
 PARTITION s2 VALUES LESS THAN (202003), 
 PARTITION s3 VALUES LESS THAN (202004), 
 PARTITION s4 VALUES LESS THAN (202005), 
 PARTITION s5 VALUES LESS THAN (202006),
 PARTITION s999 VALUES LESS THAN (MAXVALUE)
 );
```

Read and modify partitions:

<pre>
SELECT partition_name, partition_method, partition_expression 
<b>FROM information_schema.partitions </b>
WHERE table_name = 'sales'
ORDER BY partition_ordinal_position; 

ALTER TABLE sales <b>REORGANIZE PARTITION</b> s999 INTO 
    (PARTITION s6 VALUES LESS THAN (202007), 
    PARTITION s7 VALUES LESS THAN (202008), 
    PARTITION s999 VALUES LESS THAN (MAXVALUE)
    );
</pre> 

### List partitioning

```sql
PARTITION BY LIST COLUMNS (geo_region_cd)
(PARTITION ASIA VALUES IN ('CHN','JPN','IND'))

ALTER TABLE sales REORGANIZE PARTITION ASIA INTO 
(PARTITION ASIA VALUES IN ('CHN','JPN','IND', 'KOR'));
```

### Hash partitioning

The server does this by applying a *hashing function* to the column value.

```sql
PARTITION BY HASH (cust_id) 
PARTITIONS 4 
(PARTITION H1, 
 PARTITION H2, 
 PARTITION H3, 
 PARTITION H4
);
```

### Composite partitioning

If you need finer-grained control of how data is allocated to your partitions, you can employ *composite partitioning*, which allows you to use two different types of partitioning for the same table. With composite partitioning, the first partitioning method defines the partitions, and the second partitioning method defines the *subpartitions*.

```sql
CREATE TABLE sales 
 (sale_id INT NOT NULL, 
 cust_id INT NOT NULL, 
 store_id INT NOT NULL, 
 sale_date DATE NOT NULL, 
 amount DECIMAL(9,2) 
 )
 PARTITION BY RANGE (yearweek(sale_date)) 
 SUBPARTITION BY HASH (cust_id) 
 (PARTITION s1 VALUES LESS THAN (202002) 
 (SUBPARTITION s1_h1, SUBPARTITION s1_h2, SUBPARTITION s1_h3, SUBPARTITION s1_h4),
 PARTITION s2 VALUES LESS THAN (202003) 
 (SUBPARTITION s2_h1, SUBPARTITION s2_h2, SUBPARTITION s2_h3, SUBPARTITION s2_h4),
 PARTITION s3 VALUES LESS THAN (202004) 
 (SUBPARTITION s3_h1, SUBPARTITION s3_h2,
 SUBPARTITION s3_h3,
 SUBPARTITION s3_h4), 
 PARTITION s4 VALUES LESS THAN (202005) 
 (SUBPARTITION s4_h1, SUBPARTITION s4_h2, SUBPARTITION s4_h3, SUBPARTITION s4_h4),
 PARTITION s5 VALUES LESS THAN (202006) 
 (SUBPARTITION s5_h1, SUBPARTITION s5_h2, SUBPARTITION s5_h3, SUBPARTITION s5_h4),
 PARTITION s999 VALUES LESS THAN (MAXVALUE) 
 (SUBPARTITION s999_h1, SUBPARTITION s999_h2, SUBPARTITION s999_h3,
 SUBPARTITION s999_h4)
 );

SELECT * 
FROM sales PARTITION (s3);

SELECT * 
FROM sales PARTITION (s3_h3);
```

## Partitioning Benefits

One major advantage to partitioning is that you may only need to interact with as few as one partition, rather than the entire table.

If you execute a query that includes a join to a partitioned table and the query includes a condition on the partitioning column, the server can exclude any partitions that do not contain data pertinent to the query. This is known as *partitionwise joins*, and it is similar to partition pruning in that only those partitions that contain data needed by the query will be included.

From an administrative standpoint, one of the main benefits to partitioning is the ability to quickly delete data that is no longer needed. 

Another administrative advantage to partitioned tables is the ability to perform updates on multiple partitions simultaneously, which can greatly reduce the time needed to touch every row in a table.

# Clustering

*Clustering* allows multiple servers to act as a single database.

Shared-disk/shared-cache configurations: every server in the cluster has access to all disks, and data cached in one server can be accessed by any other server in the cluster. With this type of architecture, an application server could attach to any one of the database servers in the cluster, with connections automatically failing over to another server in the cluster in case of failure.

Of the commercial database vendors, Oracle is the leader in this space, with many of the world’s biggest companies using the Oracle Exadata platform to host extremely large databases accessed by thousands of concurrent users. However, even this plat‐ form fails to meet the needs of the biggest companies, which led Google, Facebook, Amazon, and other companies to blaze new trails.

# Sharding

*Sharding* partitions the data across multiple databases (called *shards*), so it is similar to table partitioning but on a larger scale and with far more complexity. If you were to employ this strategy for the social media company, you might decide to implement 100 separate databases, each one hosting the data for approximately 10 million users.

* You will need to choose a *sharding key*, which is the value used to determine to which database to connect.
* While large tables will be divided into pieces, with individual rows assigned to a single shard, smaller reference tables may need to be replicated to all shards, and a strategy needs to be defined for how reference data can be modified and changes propagated to all shards.
* If individual shards become too large (e.g., the social media company now has two billion users), you will need a plan for adding more shards and redistributing data across the shards.
* When you need to make schema changes, you will need to have a strategy for deploying the changes across all of the shards so that all schemas stay in sync.
* If application logic needs to access data stored in two or more shards, you need to have a strategy for how to query across multiple databases and also how to implement transactions across multiple databases.

# Big Data

One way to define the boundaries of big data is with the “3 Vs”:

*Volume* 

In this context, volume generally means billions or trillions of data points. 

*Velocity* 

This is a measure of how quickly data arrives. 

*Variety*

This means that data is not always structured (as in rows and columns in a rela‐ tional database) but can also be unstructured (e.g., emails, videos, photos, audio files, etc.).

So, one way to characterize big data is any system designed to handle a huge amount of data of various formats arriving at a rapid pace. 

## Hadoop 

Hadoop is best described as an *ecosystem*, or a set of technologies and tools that work together. Some of the major components of Hadoop include:

*Hadoop Distributed File System (HDFS)* 

Like the name implies, HDFS enables file management across a large number of servers.

*MapReduce*

This technology processes large amounts of structured and unstructured data by breaking a task into many small pieces that can be run in parallel across many servers.

*YARN* 

This is a resource manager and job scheduler for HDFS.

Together, these technologies allow for the storage and processing of files across hun‐ dreds or even thousands of servers acting as a single logical system. While Hadoop is widely used, querying the data using MapReduce generally requires a programmer, which has led to the development of several SQL interfaces, including Hive, Impala, and Drill.

## NoSQL and Document Databases

What happens, however, if the structure of the data isn’t known beforehand or if the structure is known but changes frequently? The answer for many companies is to combine both the data and schema definition into documents using a format such as XML or JSON and then store the documents in a database. By doing so, various types of data can be stored in the same database without the need to make schema modifications, which makes storage easier but puts the burden on query and analytic tools to make sense of the data stored in the documents.

Document databases are a subset of what are called NoSQL databases, which typically store data using a simple key-value mechanism. For example, using a document data‐ base such as MongoDB, you could utilize the customer ID as the key to store a JSON document containing all of the customer’s data, and other users can read the schema stored within the document to make sense of the data stored within.

## Cloud Computing 

Prior to the advent of big data, most companies had to build their own data centers to house the database, web, and application servers used across the enterprise. With the advent of cloud computing, you can choose to essentially outsource your data center to platforms such as Amazon Web Services (AWS), Microsoft Azure, or Google Cloud. One of the biggest benefits to hosting your services in the cloud is **instant scalability**, which allows you to quickly dial up or down the amount of computing power needed to run your services. Startups love these platforms because they can start writing code without spending any money up front for servers, storage, networks, or software licenses.

As far as databases are concerned, a quick look at AWS’s database and analytics offerings yields the following options:

* Relational databases (MySQL, Aurora, PostgreSQL, MariaDB, Oracle, and SQL Server)
* In-memory database (ElastiCache) 
* Data warehousing database (Redshift) 
* NoSQL database (DynamoDB) 
* Document database (DocumentDB) 
* Graph database (Neptune) 
* Time-series database (TimeStream) 
* Hadoop (EMR) 
* Data lakes (Lake Formation)

While relational databases dominated the landscape up until the mid-2000s, it’s pretty easy to see that companies are now mixing and matching various platforms and that relational databases may become less popular over time.

## Conclusion 

Databases are getting larger, but at the same time storage, clustering, and partitioning technologies are becoming more robust. Working with huge amounts of data can be quite challenging, regardless of the technology stack. Whether you use relational databases, big data platforms, or a variety of database servers, SQL is evolving to facilitate data retrieval from various technologies. 