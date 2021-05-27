---
title: 'Learning SQL Notes #2: Data Types'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The second note from Learning SQL"

date: "2021-05-26T01:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---
- [Character Data](#character-data)
- [Numeric Data](#numeric-data)
- [Temporal Data](#temporal-data)
- [BOUNS: Find Current Time](#bouns-find-current-time)



### Character Data

```SQL
char(20) /* fixed-length */
varchar(20) /* variable-length */
```
No easy way to constrain the length of character in **R**, but one can try `stringr::str_trunc()`.

Note:

1. If the data being loaded into a text column exceeds the maximum size for that type, the data will be truncated;
2. Trailing spaces **will not** be removed when data is loaded into the column; 
3. When using text columns for sorting or grouping, only the first 1,024 bytes are used, although this limit may be increased if necessary.


```SQL
CREATE DATABASE european_sales CHARACTER SET latin1;
```

### Numeric Data
1. Boolean: 0 False, 1 True.
2. System-generated primary keys: 1 to $\infin$, integers;
   ```SQL
   mediumint −8,388,608 to 8,388,607 
   mediumint unsigned 0 to 16,777,215

   int −2,147,483,648 to 2,147,483,647
   int unsigned 0 to 4,294,967,295

   bigint −2^63 to 2^63 - 1
   bigint unsigned 0 to 2^64 - 1
   ``` 
3. Item number: positive integers in a range;
   ```SQL
   tinyint −128 to 127 
   tinyint unsigned 0 to 255

   smallint −32,768 to 32,767 
   smallint unsigned 0 to 65,535
   ```
   unsigned takes only positive values；
4. High-precision scientific or manufacturing data; 
   ```SQL
   float( p , s ) −3.402823466E+38 to −1.175494351E-38 and 1.175494351E-38 to 3.402823466E+38

   double( p , s ) −1.7976931348623157E+308 to −2.2250738585072014E-308
   and 2.2250738585072014E-308 to 1.7976931348623157E+308 
   ```
    p, s are optional parameters, precision (the total number of allowable digits both to the left and to the right of the decimal point) and a scale (the number of allowable digits to the right of the decimal point), left digits = p - s.

### Temporal Data

* The **future date** that a particular event is expected to happen, such as shipping a customer’s order
  ```SQL
  date YYYY-MM-DD 1000-01-01 to 9999-12-31
  ```
* The date that a customer’s order **was shipped** 
  ```SQL
  datetime YYYY-MM-DD HH:MI:SS 1000-01-01 00:00:00.000000 to 9999-12-31 23:59:59.999999
  ```
* The **date and time** that a user **modified** a particular row in a table 
  ```SQL
  timestamp YYYY-MM-DD HH:MI:SS 1970-01-01 00:00:00.000000 to 2038-01-18 22:14:07.999999
  ```
* An employee’s **birth date** 
  ```SQL
  date YYYY-MM-DD 1000-01-01 to 9999-12-31
  ```
* The **year** corresponding to a row in a yearly_sales fact table in a data warehouse
  ```SQL
  year YYYY 1901-2155
  ```
* The **elapsed time** needed to complete a wiring harness on an automobile assembly line
  ```SQL
  time HHH:MI:SS −838:59:59.000000 to 838:59:59.000000
  ```

### BOUNS: Find Current Time

To find the current data/time:
```SQL
SELECT now();
/*2019-04-04 20:44:26 Timezone not included*/
```
**R** codes：
```r
sys.time()
# "2021-05-25 10:58:06 EDT", Timezone included
```

If Oracle, add `FROM dual;`;(Think about *dummy variable*!)
