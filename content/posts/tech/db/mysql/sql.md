---
title: sql 
date: "2021-01-20T17:02:50+0800"
draft: false
categories: ["database"]
---

[Order of execution of a Query](https://sqlbolt.com/lesson/select_queries_order_of_execution)


## overview

![cUpoBD](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220515/cUpoBD.jpg)

1. DQL: date query
2. DDL: data definition;
   1. action:
      1. create 
      2. drop 
      3. alter
      4. truncate;
   2. object:
      1. table
      2. index
      3. database;
      4. event;
      5. view
      6. trigger
3. DML: update/delete/insert
4. DCL:  data control
   1. grant
   2. revoke


## sql


1. write order
```sql
SELECT DISTINCT  column, AGG_FUNC(column or expression)
FROM table
    JOIN anotherTable
    WHERE constraint_expression
    GROUP BY  column
    HAVING constraint_expression
    ORDER BY  column ASC/DESC
    LIMIT count OFFSET count;
```

2. run order
   1. FROM/JOIN clause: determine the total data set;
   2. WHERE clause:
   3. GROUP BY clause
   4. HAVING clause
   5. SELECT clause
   6. DISTINCT
   7. ORDER BY clause


## 1. subquery/ nested query

```sql
-- in select
select id, (select max(prie) from t) from fruits
-- in from;
select * from (select * from t) t1
-- in where hving
select * from t1 where/having (id in (select max(id) from t1 group by date))
-- in join 
select * from t1 left join (select * from t2) t2  on 
```

### correlated subquery

vs normal subquery:

1. normal subquery would excuted once

2. correlated subquery will
   1. correlate with outer table
   2. excuted each row

```sql
SELECT column1, column2, ....
FROM table1 outer
WHERE column1 operator
                    (SELECT column1, column2
                     FROM table2
                     WHERE expr1 = 
                               outer.expr2);

```




## having vs where

both of them are used for filter datas;

```sql
SELECT `value` v FROM `table` WHERE `value`>5; -- Get 5 rows;  Unknown column 'v' in 'where clause'
SELECT `value` v FROM `table` HAVING `value`>5; -- Get 5 rows;  get;

```

1. where filter before select
2. having:filter after select;

```
EXPLAIN SELECT `value` v FROM `table` WHERE `value`>5;
+----+-------------+-------+-------+---------------+-------+---------+------+------+--------------------------+
| id | select_type | table | type  | possible_keys | key   | key_len | ref  | rows | Extra                    |
+----+-------------+-------+-------+---------------+-------+---------+------+------+--------------------------+
|  1 | SIMPLE      | table | range | value         | value | 4       | NULL |    5 | Using where; Using index |
+----+-------------+-------+-------+---------------+-------+---------+------+------+--------------------------+

EXPLAIN SELECT `value` v FROM `table` having `value`>5;
+----+-------------+-------+-------+---------------+-------+---------+------+------+-------------+
| id | select_type | table | type  | possible_keys | key   | key_len | ref  | rows | Extra       |
+----+-------------+-------+-------+---------------+-------+---------+------+------+-------------+
|  1 | SIMPLE      | table | index | NULL          | value | 4       | NULL |   10 | Using index |
+----+-------------+-------+-------+---------------+-------+---------+------+------+-------------+

```


## function


## string function
1. append string: select concat("name:",name)...

## aggregate functions

perform a caculation on mutiple rows, result in one value;

1. count
2. sum()
3. max/min
4. avg

### group

group by column
= map[column]

```
select column1, aggregation from table group by column1
```

group by  column1, column2;
map[colum + column]



## mutiple table 

join vs union vs subquery


1. vs 
   1. join: 左右连接 select t1.name, t2.age from t1 join t2 on t1.id=t2.id
   2. union: 上下连接： select name,age from t1 union  select name,age from t2;
   3. subquery:嵌套在其他selct 语句中



### join

#### 1. purpose

join columns  in other table;
> vs union: join row  in other table;

#### 2. join type


```
left.id, right.id
1,      1 
2,      2
3,      3
4       5
```
select left.id,right.id from left join right on left.id=right.id
1. left/right: list left/all right item;
   1. left: [1,2,3,4],[1,2,3,null]
   2. right: [1,2,3,5], [1,2,3,null]
2. inner: list rows only left  and right match;
   1. [1,2,3],[1,2,3]
3. outer: get all  row on left and right table;
   1. [1,2,3,4,null],[1,2,3,null,5]



4. left/right: 通过外键去join
   left: a.[id, foreignKey]->b.[id],  a.foreginkey=b.id
   right a.[id,....] <- b.[id,foregin] b.foreginkey=a.id




###  union

> join  different rows in  

## sql in practice

### 1. select max(min) value per row

+--------+------------+-------+
| type   | variety    | price |
+--------+------------+-------+
| apple  | gala       |  2.79 |
| apple  | fuji       |  0.24 |
| apple  | limbertwig |  2.87 |
| orange | valencia   |  3.59 |
| orange | navel      |  9.36 |
| pear   | bradford   |  6.05 |
| pear   | bartlett   |  2.14 |
| cherry | bing       |  2.55 |
| cherry | chelan     |  6.33 |
+--------+------------+-------+

```sql

select  group by  

```


## privilege

### mysql_secure_installation

secure the initial accunt (root)

   1. set a password for root account;
   2. remove anonymous  account;

```sql
DROP USER ''@'localhost';
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new_password');
```

### create a new user

1. create user

```shell
CREATE USER 'root'@'%' IDENTIFIED BY '123'
```

2. assign privilege

GRANT privilege on db.table to 'user@address'

```shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
```

privilege type:

1. ALL PRIVILEGES
2. CREATE
3. DROP
4. DELETE
5. INSERT
6. SELECT
7. UPDATE
8. GRANT





## DDL




### 1. constraint
1. rule of the data column;

2. list:
   1. value:
      1. not null
      2. default 0
   2. key:
      1. unique  ke
         `UNIQUE KEY [name] (field,field2...)`
      2. primary key 

      3. foreign key
         `FOREIGN KEY (field) REFERENCES tablename(field)`
      4. index key
         `KEY [name] (field1, field2,)`
   3. check
      ```      
      constraint c1_nozero check (age <> 0)
      ```
      

### create table

```
CREATE TABLE  XXX(
   names Type 


)

```
