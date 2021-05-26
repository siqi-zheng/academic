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
- [字符串](#字符串)
- [数值](#数值)
- [日期数据](#日期数据)
- [BOUNS: 找到当前时间](#bouns-找到当前时间)



### 字符串

```SQL
char(20) /* fixed-length */
varchar(20) /* variable-length */
```
No easy way to constrain the length of character in **R**, but one can try `stringr::str_trunc()`.

Note:

1. 长度超过限制就会被删掉；
2. 前后空格**不会**被删掉；
3. sort/group只会用字符串的前1024bytes 。

```SQL
CREATE DATABASE european_sales CHARACTER SET latin1;
```

### 数值
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
   unsigned只取正值；
4. High-precision scientific or manufacturing data; 
   ```SQL
   float( p , s ) −3.402823466E+38 to −1.175494351E-38 and 1.175494351E-38 to 3.402823466E+38

   double( p , s ) −1.7976931348623157E+308 to −2.2250738585072014E-308
   and 2.2250738585072014E-308 to 1.7976931348623157E+308 
   ```
    p, s are optional parameters, precision (the total number of allowable digits both to the left and to the right of the decimal point) and a scale (the number of allowable digits to the right of the decimal point), left digits = p - s.

### 日期数据

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

### BOUNS: 找到当前时间

To find the current data/time:
```SQL
SELECT now();
/*2019-04-04 20:44:26 不含时区*/
```
类比**R**的代码：
```r
sys.time()
# "2021-05-25 10:58:06 EDT", 含时区
```

If Oracle, add `FROM dual;`;(Think about *dummy variable*!)