---
title: 'Learning SQL Notes #6: Data Generation, Manipulation, and Conversion'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The sixth note from Learning SQL"

date: "2021-06-04T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
--- 


- [Working with String Data](#working-with-string-data)
  - [String Generation](#string-generation)
    - [Including single quotes](#including-single-quotes)
    - [Including special characters](#including-special-characters)
  - [String Manipulation](#string-manipulation)
    - [String functions that return numbers](#string-functions-that-return-numbers)
- [Working with Numeric Data](#working-with-numeric-data)
  - [Performing Arithmetic Functions & Controlling Number Precision & Handling Signed Data](#performing-arithmetic-functions--controlling-number-precision--handling-signed-data)
- [Working with Temporal Data](#working-with-temporal-data)
  - [Dealing with Time Zones](#dealing-with-time-zones)
  - [Generating Temporal Data](#generating-temporal-data)
    - [String representations of temporal data](#string-representations-of-temporal-data)
    - [String-to-date conversions](#string-to-date-conversions)
  - [Manipulating Temporal Data](#manipulating-temporal-data)
    - [Temporal functions that return dates](#temporal-functions-that-return-dates)
    - [Temporal functions that return strings](#temporal-functions-that-return-strings)
    - [Temporal functions that return numbers](#temporal-functions-that-return-numbers)
  - [Conversion Functions](#conversion-functions)
- [Appendix for Codes](#appendix-for-codes)

## Working with String Data

### String Generation

Types:

CHAR 
    Holds fixed-length, blank-padded strings.

varchar 
    Holds variable-length strings. 

text (MySQL and SQL Server) or clob (Oracle Database) 
    Holds very large variable-length strings (generally referred to as documents in this context).

```sql
CREATE TABLE string_tbl 
(char_fld CHAR(30), 
vchar_fld VARCHAR(30), 
text_fld TEXT
);

INSERT INTO string_tbl (char_fld, vchar_fld, text_fld) 
VALUES ('This is char data',
'This is varchar data',
'This is text data');
```

If you want to have a longer string, you can
```sql
UPDATE string_tbl 
SET vchar_fld = 'This is a piece of extremely long varchar data';
```

but then:

```
ERROR 1406 (22001): Data too long for column 'vchar_fld' at row 1
```

NOTE: Since MySQL 6.0, the default behavior is now “strict” mode, which means that exceptions are thrown when problems arise, whereas in older versions of the server **the string would have been truncated and a warning issued**. 

```sql
SELECT @@session.sql_mode;
SET sql_mode='ansi'; /*Go back to the older ver.*/
SELECT @@session.sql_mode;
```

Now extra will be truncated.

#### Including single quotes

```sql
SELECT quote(text_fld) 
FROM string_tbl;
```

Output:

|               QUOTE(text_fld)               |
| :-----------------------------------------: |
| 'This string didn\'t work, but it does now' |


#### Including special characters

The SQL Server and MySQL servers include the built-in function `char()` so that you can build strings from any of the 255 characters in the ASCII character set (Oracle Database users can use the `chr()` function).

```sql
SELECT CHAR(128,129,130,131,132,133,134,135,136,137);
```

Output:

| CHAR(128,129,130,131,132,133,134,135,136,137) |
| :-------------------------------------------: |
|                  Çüéâäàåçêë                   |

**R** codes: 
```r
coderange <- c(128,129,130,131,132,133,134,135,136,137)
rawToChar(as.raw(coderange),multiple=TRUE)
```

You can also concatenate two strings:

```sql
SELECT CONCAT('danke sch', CHAR(148), 'n');
```

Output:

| CONCAT('danke sch', CHAR(148), 'n') |
| :---------------------------------: |
|             danke schön             |

**R** codes: 
```r
paste('danke sch', rawToChar(as.raw(148)), 'n')
paste0()
```
See: https://www.r-bloggers.com/2011/03/ascii-code-table-in-r/

* Oracle Database/PostgreSQL users can use the concatenation operator (`||`) instead of the `concat()` function, as in:
```sql
SELECT 'danke sch' || CHR(148) || 'n' FROM dual;
```

* SQL Server does not include a `concat()` function, so you will need to use the concatenation operator (+), as in:
```sql
SELECT 'danke sch' + CHAR(148) + 'n'
```

### String Manipulation

#### String functions that return numbers

To find the length of a string:

```sql
LENGTH()

SELECT LENGTH(char_fld) char_length, 
LENGTH(vchar_fld) varchar_length, 
LENGTH(text_fld) text_length
FROM string_tbl;
```

**R** codes: 

```r
length()
```

To find the index of a character in a string:

```sql
POSITION()

SELECT POSITION('characters' IN vchar_fld)
FROM string_tbl;
```

**R** codes: 

```r
match('y',x)
which('y' %in% x)
```

Note: When working with databases that the **first** character in a string is at position **1**. A return value of **0** from `instr()` indicates that the substring **could not be found**, not that the substring was found at the first position in the string.


If you want to start your search at something **other than the first character** of your target string, you will need to use the `locate()` function, which is similar to the `position()` function except that it allows an optional **third parameter**, which is used to define the search’s start position. The `locate()` function is also proprietary, whereas the `position()` function is part of the SQL:2003 standard.

```sql
SELECT LOCATE('is', vchar_fld, 5)
FROM string_tbl;
```

**R** codes: 

```r
match('y',x[5:])
which('y' %in% x[5:])
```

Oracle Database 
`instr()`: Mimics the `position()` function when provided with two arguments and mimics the `locate()` function when provided with three arguments. 

SQL Server
`charindx()`: similar to Oracle’s `instr()` function.

`strcmp()` (MySQL ONLY) takes two strings as arguments and returns one of the following:

* −1 if the first string comes before the second string in sort order 
* 0 if the strings are identical
* 1 if the first string comes after the second string in sort order

```sql
SELECT vchar_fld
FROM string_tbl
ORDER BY vchar_fld;
```
| vchar_fld |
| :-------: |
|   12345   |
|   abcd    |
|  QRSTUV   |
|  qrstuv   |
|    xyz    |

```sql
SELECT STRCMP('12345','12345') 12345_12345,
STRCMP('abcd','xyz') abcd_xyz, 
STRCMP('abcd','QRSTUV') abcd_QRSTUV, 
STRCMP('qrstuv','QRSTUV') qrstuv_QRSTUV, /*Case insensitive*/
STRCMP('12345','xyz') 12345_xyz,
STRCMP('xyz','qrstuv') xyz_qrstuv;
```
| 12345_12345 | abcd_xyz | abcd_QRSTUV | qrstuv_QRSTUV | 12345_xyz | xyz_qrstuv |
| :---------- | -------- | ----------- | ------------- | --------- | ---------: |
| 0           | −1       | −1          | 0             | −1        |          1 |


Add or replace characters in the *middle* of a string：
`insert()`
4 parameters: the original string, the start position, the number of characters to replace (0 for inserting a string), and the replacement string.

```sql
SELECT INSERT('goodbye world', 9, 0, 'cruel ') string;
/*goodbye cruel world*/
SELECT INSERT('goodbye world', 1, 7, 'hello') string;
/*hello world*/
SELECT SUBSTRING('goodbye cruel world', 9, 5);
/*cruel*/
```

For other SQL,

```sql
/*Oracle*/
SELECT REPLACE('goodbye world', 'goodbye', 'hello') FROM dual;
/*hello world*/
SELECT substr('goodbye cruel world', 9, 5);
/*cruel*/

/*SQL Server*/
SELECT STUFF('hello world', 1, 5, 'goodbye cruel')
/*goodbye cruel world*/
SELECT SUBSTRING('goodbye cruel world', 9, 5);
/*cruel*/
```

## Working with Numeric Data

```sql
SELECT (37 * 59) / (78 - (8 * 6));
```

### Performing Arithmetic Functions & Controlling Number Precision & Handling Signed Data

| Function name |                     Description |
| :------------ | ------------------------------: |
| acos( x )     |  Calculates the arc cosine of x |
| asin( x )     |    Calculates the arc sine of x |
| atan( x )     | Calculates the arc tangent of x |
| cos( x )      |      Calculates the cosine of x |
| sin( x )      |        Calculates the sine of x |
| tan( x )      |     Calculates the tangent of x |
| cot( x )      |   Calculates the cotangent of x |
| exp( x )      |                   Calculates ex |
| ln( x )       | Calculates the natural log of x |
| sqrt( x )     | Calculates the square root of x |

Some useful functions in R and SQL (See Appendix for full results):

| SQL           |         R |
| :------------ | --------: |
| MOD( x )      |        %% |
| POW( x )      |         ^ |
| CEIL( x )     | ceiling() |
| FLOOR( x )    |   floor() |
| ROUND( x )    |   round() |
| TRUNCATE( x ) |   trunc() |
| SIGN( x )     |    sign() |
| ABS( x )      |     abs() |

## Working with Temporal Data

### Dealing with Time Zones

```sql
/*MySQL*/
SELECT @@global.time_zone, @@session.time_zone;
SET time_zone = 'Europe/Zurich';

/*Oracle Database*/
ALTER SESSION TIMEZONE = 'Europe/Zurich'
```
From:

| @@global.time_zone | @@session.time_zone |
| :----------------- | ------------------: |
| SYSTEM             |              SYSTEM |

To:

| @@global.time_zone | @@session.time_zone |
| :----------------- | ------------------: |
| SYSTEM             |       Europe/Zurich |

**R** codes: 

```r
Sys.timezone()
Sys.setenv(TZ = "Europe/Zurich")
```

### Generating Temporal Data 

You can generate temporal data via any of the following means:

* Copying data from an existing date, datetime, or time column 
*  Executing a built-in function that returns a date, datetime, or time 
*  Building a string representation of the temporal data to be evaluated by the server

#### String representations of temporal data

| Component | Definition              |                         Range |
| :-------- | ----------------------- | ----------------------------: |
| YYYY      | Year, including century |                  1000 to 9999 |
| MM        | Month                   | 01 (January) to 12 (December) |
| DD        | Day                     |                      01 to 31 |
| HH        | Hour                    |                Range 00 to 23 |
| HHH       | Hours                   |                   −838 to 838 |
| MI        | (elapsed) Minute        |                      00 to 59 |
| SS        | Second                  |                      00 to 59 |

| Type      |      Default format |
| :-------- | ------------------: |
| date      |          YYYY-MM-DD |
| datetime  | YYYY-MM-DD HH:MI:SS |
| timestamp | YYYY-MM-DD HH:MI:SS |
| time      |           HHH:MI:SS |

#### String-to-date conversions

* A simple query that returns a datetime value using the `cast()` function

| SQL                                            |            R (lubridate) |
| :--------------------------------------------- | -----------------------: |
| CAST('2019-09-17 15:30:00' AS DATETIME)        |            as_datetime() |
| STR_TO_DATE('September 17, 2019', '%M %d, %Y') | as.Date(..., format=...) |
| CAST('2019-09-17' AS DATE)                     |                as.Date() |
| CAST('108:17:57' AS TIME)                      |             as.POSIXlt() |

```sql
/*MySQL*/
SELECT str_to_date();

/*Oracle Database*/
SELECT to_date();

/*SQL server*/
SELECT convert();

/*Current System Time*/
SELECT CURRENT_DATE(), CURRENT_TIME(), CURRENT_TIMESTAMP();
```

Common notations for both R and SQL:

| Format component |                       Description |
| :--------------- | --------------------------------: |
| %M               |  Month name (January to December) |
| %m               |          Month numeric (01 to 12) |
| %d               |            Day numeric (01 to 31) |
| %j               |          Day of year (001 to 366) |
| %W               | Weekday name (Sunday to Saturday) |
| %Y               |          Year, four-digit numeric |
| %y               |           Year, two-digit numeric |
| %H               |                   Hour (00 to 23) |
| %h               |                   Hour (01 to 12) |
| %i               |                Minutes (00 to 59) |
| %s               |                Seconds (00 to 59) |
| %f               |   Microseconds (000000 to 999999) |
| %p               |                      A.M. or P.M. |
 
### Manipulating Temporal Data

**Interval types for `DATE_ADD()` and `EXTRACT()`**

| Interval name |                                             Description |
| :------------ | ------------------------------------------------------: |
| second        |                                       Number of seconds |
| minute        |                                       Number of minutes |
| hour          |                                         Number of hours |
| day           |                                          Number of days |
| month         |                                        Number of months |
| year          |                                         Number of years |
| minute_second |         Number of minutes and seconds, separated by “:” |
| hour_second   | Number of hours, minutes, and seconds, separated by “:” |
| year_month    |            Number of years and months, separated by “-” |

#### Temporal functions that return dates

The same result can be performed on three different servers:

```sql
/*MySQL*/
UPDATE employee 
SET birth_date = DATE_ADD(birth_date, INTERVAL '9-11' YEAR_MONTH)
WHERE emp_id = 4789;

/*Oracle Database*/
UPDATE employee 
SET birth_date = ADD_MONTHS(birth_date, 119)
WHERE emp_id = 4789;

/*SQL server*/
UPDATE employee 
SET birth_date = DATEADD(MONTH, 119, birth_date)
WHERE emp_id = 4789
```

#### Temporal functions that return strings

Some other functions for temporal data:

```sql
/*MySQL*/
SELECT LAST_DAY('2019-09-17'); /*Extract last day of Sept*/
SELECT DAYNAME('2019-09-18'); /*Wednesday*/
SELECT EXTRACT(YEAR FROM '2019-09-18 22:19:05'); /*2019*/

/*SQL Server*/
SELECT DATEPART(YEAR, GETDATE())
```

#### Temporal functions that return numbers

```sql
SELECT DATEDIFF('2019-09-03', '2019-06-21');
/*74*/

SELECT DATEDIFF('2019-09-03 23:59:59', '2019-06-21 00:00:01');
/*74, time has no effects*/

SELECT DATEDIFF('2019-06-21', '2019-09-03');
/*-74*/

/*SQL Server*/
SELECT DATEDIFF(DAY, '2019-06-21', '2019-09-03')
```

### Conversion Functions

```sql
SELECT CAST('1456328' AS SIGNED INTEGER);
/*1456328*/

SELECT CAST('999ABC111' AS UNSIGNED INTEGER);
/*999 with warnings about truncation*/
```


## Appendix for Codes

```sql
SELECT MOD(10,4);
/*2*/
SELECT MOD(20.75,4); /*Real argument*/
/*0.75*/
SELECT POW(2,8);
/*256*/

SELECT CEIL(72.445), FLOOR(72.445);
/*73  72*/
SELECT CEIL(72.000000001), FLOOR(72.999999999);
/*73  72*/

SELECT ROUND(72.49999), ROUND(72.5), ROUND(72.50001);
/*72  73  73*/

SELECT ROUND(72.0909, 1), ROUND(72.0909, 2), ROUND(72.0909, 3);
/*72.1  72.09  72.091*/
SELECT TRUNCATE(72.0909, 1), TRUNCATE(72.0909, 2), TRUNCATE(72.0909, 3);
/*72.0  72.09  72.090*/

/*SQL Server*/
SELECT ROUND(72.0909, 1, 1)
```

**R** codes: 

```r
%%
^
ceiling()
floor()
round()
trunc()
```

```sql
SELECT account_id, SIGN(balance), ABS(balance) 
FROM account;
```

**R** codes: 

```r
sign()
abs()
```

Hope I can finish this before July. Stay safe.

![](2.gif)
