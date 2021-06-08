---
title: 'Learning SQL Notes #10: Transactions'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The tenth note from Learning SQL"

date: "2021-06-08T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

- [Multiuser Databases](#multiuser-databases)
  - [Locking](#locking)
  - [Lock Granularities](#lock-granularities)
- [What Is a Transaction?](#what-is-a-transaction)
  - [Starting a Transaction](#starting-a-transaction)
  - [Ending a Transaction](#ending-a-transaction)
  - [Transaction Savepoints](#transaction-savepoints)
    - [Choosing a Storage Engine](#choosing-a-storage-engine)


Transactions: Mechanism used to group a set of SQL statements together such that either all or none of the statements succeed.

# Multiuser Databases

## Locking 
Locks are the mechanism the database server uses to **control simultaneous use** of data resources. When some portion of the database is locked, any other users wishing to modify (or possibly read) that data must wait until the lock has been released. Most database servers use one of two locking strategies:

* Database writers must request and receive from the server a write lock to modify data, and database readers must request and receive from the server a read lock to query data. While multiple users can read data simultaneously, only one write lock is given out at a time for each table (or portion thereof), and read requests are blocked until the write lock is released. $\Rightarrow$ long wait times if there are many concurrent read and write requests. (Microsoft SQL Server/MySQL)

* Database writers must request and receive from the server a write lock to modify data, but readers do not need any type of lock to query data. Instead, the server ensures that a reader sees a consistent view of the data (the data seems the same even though other users may be making modifications) from the time her query begins until her query has finished. This approach is known as *versioning*. $\Rightarrow$ problematic if there are long-running queries while data is being modified. (Oracle Database/MySQL)

## Lock Granularities

*Table locks* $\Rightarrow$ less bookkeeping, longer waiting time
Keep multiple users from modifying data in the same table simultaneously

*Page locks* 
Keep multiple users from modifying data on the same page (a page is a segment of memory generally in the range of 2 KB to 16 KB) of a table simultaneously

*Row locks* $\Rightarrow$ More bookkeeping, shorter waiting time
Keep multiple users from modifying the same row in a table simultaneously

SQL Server will, under certain circumstances, *escalate* locks from row to page, and from page to table, whereas Oracle Database will never escalate locks.

# What Is a Transaction?

Problems occur when one of the ideal situations fails:

* Database servers do not enjoy 100% uptime
* Users do not always allow programs to finish executing
* Applications do not always complete without encountering fatal errors that halt execution

*Transaction* is a device for grouping together multiple SQL statements such that either all or none of the statements succeed (a property known as atomicity). 

Ex:

If you attempt to transfer \$500 from your savings account to your checking account, you would be a bit upset if the money were successfully withdrawn from your savings account but never made it to your checking account. Whatever the reason for the failure (the server was shut down for maintenance, the request for a page lock on the account table timed out, etc.), you want your $500 back. To protect against this kind of error, the program that handles your transfer request would first **begin a transaction**, then issue the SQL statements needed to move the money from your savings to your checking account, and, **if everything succeeds**, end the transaction by issuing the **commit** command. If something **unexpected** **happens**, however, the program would issue a **rollback** command, which instructs the server to undo all changes made since the transaction began.

## Starting a Transaction

Database servers handle transaction creation in one of two ways: 

* An active transaction is always associated with a database session, so there is no need or method to explicitly begin a transaction. When the current transaction ends, the server automatically begins a new transaction for your session. *You can undo some changes.* (Oracle Database)

* Unless you explicitly begin a transaction, individual SQL statements are automatically committed independently of one another. To begin a transaction, you must first issue a command. (Microsoft SQL Server/MySQL)

The SQL:2003 standard includes a `start transaction` command to be used when you want to explicitly begin a transaction. While MySQL conforms to the standard, SQL Server users must instead issue the command `begin transaction`. With both servers, until you explicitly begin a transaction, you are in what is known as *autocommit mode*, which means that individual statements are automatically committed by the server. 

A word of advice: shut off autocommit mode each time you log in, and get in the habit of running all of your SQL statements within a transaction.

Both MySQL and SQL Server allow you to turn off autocommit mode for individual sessions, in which case the servers will act just like Oracle Database regarding transactions. With SQL Server, you issue the following command to disable autocommit mode:

`SET IMPLICIT_TRANSACTIONS ON`

MySQL allows you to disable autocommit mode via the following: 

`SET AUTOCOMMIT=0`

Once you have left autocommit mode, all SQL commands take place within the scope of a transaction and must be explicitly committed or rolled back.

## Ending a Transaction

End with `commit` if yes and `rollback` if no.

Some scenarios in practice:

* The server shuts down, in which case your transaction will be rolled back automatically when the server is restarted. ✔

* You issue an SQL schema statement, such as alter table, which will cause the current transaction to be committed and a new transaction to be started.
  * be careful that the state‐ ments that comprise a unit of work are not inadvertently broken up into multiple transactions by the server！

* You issue another start transaction command, which will cause the previous transaction to be committed. ✔

* The server prematurely ends your transaction because the server detects a dead‐ lock and decides that your transaction is the culprit. In this case, the transaction
will be rolled back, and you will receive an error message. 
  * Most of the time, the terminated transaction can be restarted and will succeed without encountering another deadlock situation.  
  `Message: Deadlock found when trying to get lock; try restarting transaction`

## Transaction Savepoints

You may not want to undo *all* of the work that has transpired. For these situations, you can establish one or more *savepoints* 

```sql
SAVEPOINT my_savepoint;
```

within a transaction and use them to roll back to a particular location within your transaction 

```sql
ROLLBACK TO SAVEPOINT my_savepoint;
```

rather than rolling all the way back to the start of the transaction.

### Choosing a Storage Engine

When using Oracle Database or Microsoft SQL Server, a single set of code is respon‐ sible for low-level database operations, such as retrieving a particular row from a table based on primary key value. The MySQL server, however, has been designed so that multiple storage engines may be utilized to provide low-level database functionality, including resource locking and transaction management. As of version 8.0, MySQL includes the following storage engines:

*MyISAM* 
A nontransactional engine employing table locking

*MEMORY* 
A nontransactional engine used for in-memory tables

*CSV* 
A transactional engine that stores data in comma-separated files 

*InnoDB* 
A transactional engine employing row-level locking 

*Merge*
A specialty engine used to make multiple identical *MyISAM* tables appear as a single table (a.k.a. table partitioning)

*Archive*
A specialty engine used to store large amounts of unindexed data, mainly for archival purposes

MySQL is flexible enough to allow you to choose a storage engine on a table-by-table basis.

You may explicitly specify a storage engine when creating a table, or you can change an existing table to use a different engine.

```sql
show table status like 'customer' \G;
/*Second row: Engine: InnoDB*/
ALTER TABLE customer ENGINE = INNODB;
```

One example is shown below:

```sql
START TRANSACTION; 
UPDATE product
SET date_retired = CURRENT_TIMESTAMP() 
WHERE product_cd = 'XYZ';

SAVEPOINT before_close_accounts; 

UPDATE account
SET status = 'CLOSED', close_date = CURRENT_TIMESTAMP(), last_activity_date = CURRENT_TIMESTAMP() 
WHERE product_cd = 'XYZ';

ROLLBACK TO SAVEPOINT before_close_accounts;
COMMIT;
/*The net effect of this transaction is that the mythical XYZ product is retired but none of the accounts are closed.*/
```

When using savepoints, remember the following: 

* Despite the name, nothing is saved when you create a savepoint. You must even‐ tually issue a commit if you want your transaction to be made permanent.

* If you issue a rollback without naming a savepoint, all savepoints within the transaction will be ignored, and the entire transaction will be undone.

If you are using *SQL Server*, you will need to use the proprietary command `save transaction` to create a savepoint and `rollback transaction` to roll back to a savepoint, with each command being followed by the savepoint name.