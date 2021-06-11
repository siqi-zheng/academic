---
title: 'Learning SQL Notes #16: SQL and Big Data'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The sixteenth note from Learning SQL"

date: "2021-06-11T15:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Introduction to Apache Drill](#introduction-to-apache-drill)
- [Querying Files Using Drill](#querying-files-using-drill)
- [Querying MySQL Using Drill](#querying-mysql-using-drill)
- [Querying MongoDB Using Drill](#querying-mongodb-using-drill)
- [Drill with Multiple Data Sources](#drill-with-multiple-data-sources)
- [Future of SQL](#future-of-sql)

The data landscape has changed quite a bit over the past decade, and SQL is changing to meet the needs of today’s rapidly evolving environments. Many organizations that had used relational databases exclusively just a few years ago are now also housing data in Hadoop clusters, data lakes, and NoSQL databases. At the same time, companies are struggling to find ways to gain insights from the ever-growing volumes of data, and the fact that this data is now spread across multiple data stores, perhaps both on-site and in the cloud, makes this a daunting task. 

Because SQL is used by millions of people and has been integrated into thousands of applications, it makes sense to leverage SQL to harness this data and make it actionable. Over the past several years, a new breed of tools has emerged to enable SQL access to structured, semi-structured, and unstructured data: tools such as Presto, Apache Drill, and Toad Data Point. This chapter explores one of these tools, Apache Drill, to demonstrate how data in different formats and stored on different servers can be brought together for reporting and analysis.

# Introduction to Apache Drill

Compelling features:

* Facilitates queries across multiple data formats, including delimited data, JSON, Parquet, and log files
* Connects to relational databases, Hadoop, NoSQL, HBase, and Kafka, as well as specialized data formats such as PCAP, BlockChain, and others
* Allows creation of custom plug-ins to connect to most any other data store 
* Requires no up-front schema definitions 
* Supports the SQL:2003 standard 
* Works with popular business intelligence (BI) tools like Tableau and Apache Superset
Using Drill, you can connect to any number of data sources and begin querying, without the need to first set up a metadata repository.

# Querying Files Using Drill

Let’s start by using Drill to query data in a file. Drill understands how to read several different file formats, including packet capture (PCAP) files, which are in binary for‐ mat and contain information about packets traveling over a network. All I have to do when I want to query a PCAP file is to configure Drill’s dfs (distributed filesystem) plug-in to include the path to the directory containing my files, and I’m ready to write queries.

Drill includes partial support for information_schema, so you can find out high-level information about the data files in your workspace:

<pre>
SELECT file_name, is_directory, is_file, permission
FROM <b>information_schema.`files`</b>
WHERE schema_name = 'dfs.data';

SELECT * FROM dfs.data.`attack-trace.pcap` 
<b>WHERE 1=2;</b> # To see the column name
</pre>

Counts the number of packets sent from each IP address to each destination port:

<pre>
SELECT src_ip, dst_port,
count(*) AS packet_count 
FROM dfs.data.`attack-trace.pcap`
GROUP BY src_ip, dst_port;
</pre>

Aggregates packet information for each second:

<pre>
SELECT trunc(extract(second from `timestamp`)) as packet_time,
count(*) AS num_packets, 
sum(packet_length) AS tot_volume 
FROM dfs.data.`attack-trace.pcap`
GROUP BY trunc(extract(second from `timestamp`));
</pre>

Put backticks (`) around timestamp because it is a reserved word.

You can query files stored locally, on your network, in a distributed filesystem, or in the cloud. Drill has built-in support for many file types, but you can also build your own plug-in to allow Drill to query any type of file. 

# Querying MySQL Using Drill

Why Apache Drill? Because you can write queries using Drill that combine data from different sources, so you might write a query that joins data from MySQL, Hadoop, and comma-delimited files, for example.

The first step is to choose a database: 

<pre>
apache drill (information_schema)> <b>use mysql.sakila</b>;

<b>show tables;</b>
</pre>

Simple joins, group by, order and having work for Drill as well. However, Drill works with many relational databases, not just MySQL, so some features of the language may differ (e.g., data conversion functions). For more information, read [Drill’s documentation about their SQL implementation](http://drill.apache.org/docs/sql-reference/).

# Querying MongoDB Using Drill

After using Drill to query the sample Sakila data in MySQL, the next logical step is to convert the Sakila data to another commonly used format, store it in a nonrelational database, and use Drill to query the data. I decided to convert the data to JSON and store it in MongoDB, which is one of the more popular NoSQL platforms for document storage. Drill includes a plug-in for MongoDB and also understands how to read JSON documents, so it was relatively easy to load the JSON files into Mongo and begin writing queries.

After the JSON files have been loaded, the Mongo database contains two collections (films and customers), and the data in these collections spans nine different tables from the MySQL Sakila database. 

Group the data by rating and actor:

<pre>
SELECT g_pg_films.Rating,
g_pg_films.actor_list.`First name` first_name,
g_pg_films.actor_list.`Last name` last_name,
count(*) num_films
FROM
(SELECT f.Rating, flatten(Actors) actor_list
FROM films f
WHERE f.Rating IN ('G','PG')
) g_pg_films
GROUP BY g_pg_films.Rating,
g_pg_films.actor_list.`First name`,
g_pg_films.actor_list.`Last name`
HAVING count(*) > 9;
</pre>

The query should return all customers who have spent more than $80 to rent films rated either G or PG. 

<pre>
SELECT first_name, last_name, 
sum(cast(cust_payments.payment_data.Amount 
as decimal(4,2))) tot_payments
FROM
(SELECT cust_data.first_name,
cust_data.last_name,
f.Rating,
flatten(cust_data.rental_data.Payments)
payment_data
FROM films f
INNER JOIN
(SELECT c.`First Name` first_name,
c.`Last Name` last_name, flatten(c.Rentals) rental_data
FROM customers c
) cust_data
 ON f._id = cust_data.rental_data.filmID
WHERE f.Rating IN ('G','PG')
) cust_payments
GROUP BY first_name, last_name
HAVING
sum(cast(cust_payments.payment_data.Amount as decimal(4,2))) > 80;
</pre>

The innermost query, which I named cust_data, flattens the Rentals list so that the cust_payments query can join to the films collection and also flatten the Payments list. The outermost query groups the data by customer name and applies a having clause to filter out customers who spent $80 or less on films rated G or PG.

# Drill with Multiple Data Sources

As long as Drill is configured to connect to both databases, you just need to describe where to find the data. 

<pre>
<b>FROM mysql.sakila.film f</b>
<b>FROM mongo.sakila.customers c</b>
</pre>

# Future of SQL 

The future of relational databases is somewhat unclear. It is possible that the big data technologies of the past decade will continue to mature and gain market share. It’s also possible that a new set of technologies will emerge, overtaking Hadoop and NoSQL, and taking additional market share from relational databases. However, most companies still run their core business functions using relational databases, and it should take a long time for this to change.

The future of SQL seems a bit clearer, however. While the SQL language started out as a mechanism for interacting with data in relational databases, tools like Apache Drill act more like an abstraction layer, facilitating the analysis of data across various database platforms. In this author’s opinion, this trend will continue, and SQL will remain a critical tool for data analysis and reporting for many years.