---
title: "mysql"
date: 2020-03-30T21:26:51+08:00
draft: false
tags: ["mysql","index","lock"]
---

[数据库索引为什么使用B+树？](https://www.jianshu.com/p/4dbbaaa200c4)

[MySQL索引背后的数据结构及算法原理](https://blog.codinglabs.org/articles/theory-of-mysql-index.html)

[1分钟了解MyISAM与InnoDB的索引差异](https://database.51cto.com/art/201808/582172.html)

[Introduction of B-Tree](https://www.geeksforgeeks.org/introduction-of-b-tree-2/)

[我必须得告诉大家的MySQL优化原理](https://www.jianshu.com/p/d7665192aaaf)

[万字总结：学习MySQL优化原理，这一篇就够了！](https://dbaplus.cn/news-155-1531-1.html)

[MySQL索引原理及BTree（B-/+Tree）结构详解](https://blog.csdn.net/u013967628/article/details/84305511#MyISAM索引实现)

[Understanding MySQL EXPLAIN](https://www.taogenjia.com/2020/06/08/mysql-explain/)

[一篇文章让你搞懂Mysql InnoDB内存结构](https://blog.csdn.net/m0_48795607/article/details/119715958)

[Indexing and Query Processing](https://courses.cs.duke.edu/spring17/compsci316/lectures/15-index-qp.pdf)

[MySQL索引原理及慢查询优化](https://tech.meituan.com/2014/06/30/mysql-index.html)

[MySQL redo & undo log-这一篇就够了](https://www.jianshu.com/p/336e4995b9b8)

[Surrogate Key vs Natural Key Differences and When to Use in SQL Server](https://www.mssqltips.com/sqlservertip/5431/surrogate-key-vs-natural-key-differences-and-when-to-use-in-sql-server/)

[Surrogate Key in SQL Server](https://www.c-sharpcorner.com/UploadFile/ff2f08/surrogate-key-in-sql-server/)

[小胖问我：MySQL 事务与 MVCC 原理？](https://segmentfault.com/a/1190000039809030)

[数据库基础（四）Innodb MVCC实现原理](https://zhuanlan.zhihu.com/p/52977862)

[MySQL gap lock, next key lock by example](https://tkstone.blog/2019/06/27/mysql-gap-lock-next-key-lock-by-example/)

[MySQL的锁机制 - 记录锁、间隙锁、临键锁](https://zhuanlan.zhihu.com/p/48269420)

[MySQL探秘(七):InnoDB行锁算法](https://juejin.cn/post/6844903735500472333)

[手把手教你分析Mysql死锁问题](https://segmentfault.com/a/1190000037510033)

[完全掌握MySQL原理篇之InnoDB存储引擎架构设计](https://www.superweb999.com/article/160531.html)

[简单谈谈MySQL的两阶段提交](https://blog.csdn.net/qq_33591903/article/details/122030252)

[MySQL - order by 出现 using filesort根因分析及优化](https://blog.51cto.com/u_15239532/4569568)

[The physical structure of records in InnoDB](https://blog.jcole.us/2013/01/10/the-physical-structure-of-records-in-innodb/)

[MySQL中IS NULL、IS NOT NULL、!=不能用索引？胡扯！
](https://juejin.cn/post/6844903921450745863#comment)
[带你了解 MySQL Binlog 不为人知的秘密](https://www.cnblogs.com/rickiyang/p/13841811.html)

[The basics of InnoDB space file layout](https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/)

[庖丁解InnoDB之UNDO LOG](https://www.1024sou.com/article/494101.html)

[MySQL三大日志(binlog、redo log和undo log)详解](https://javaguide.cn/database/mysql/mysql-logs.html#%E5%86%99%E5%85%A5%E6%9C%BA%E5%88%B6)

[MySQL 主备切换以及读写分离](https://blog.csdn.net/weixin_47061482/article/details/115212684)

[MySQL锁机制，行锁竟然加在索引上！！](https://blog.csdn.net/qq_40174198/article/details/111835482)

[一文带你搞懂MySql的各种锁](https://www.mdnice.com/writing/0ea2bfaac597469c847c0f666c88a702)

## table  basic

### variabels

1. query variables;

   ```c
   show [gloabl/session] variables;
   show varaibles like '%xx%';
   show engines: list all engines
   ```

2. set variables;

   ```c
   set @@[global/session].xxxx=xxx;
   ```

3. general variables
   1. select @@datadir:  mysql data;

### case sensitivity

1. NO: column, index,event name;
2. YES: database name, table name;

### charset, collate

1. charset
   1. uft-8: 1-3 byte; represent BMP
      > emoji
   2. utf8mb4: 1-4byte; true utf-8; repressnt BMP and supplementary plane

2. collate: 字符集的比较和排序
   1. utf8mb4_0900_ai_ci
      1. ci: case insensitivity 大小写无关
      2. ai:: ancent insensitivity  发音无关；

### backticks

1. why use
   1. the filed is a keyword(desc)  or contain space
2. why not use
   1. is not a sQL standard,not all RDBMS support it

## key

#### functional dependencies

1. 函数依赖；x->y, 输出x,必然得出y;
   1. x:determinant;
   2. y:dependent;

#### super key

![](https://techdifferences.com/wp-content/uploads/2016/12/super-keys.jpg)

a set of field which help get rest the rest value of a row record

abcdef

if (a,b) ==> abcdef; (a,b) is the super key;

#### candidate key

1. what？
   if a super key's proper sets have no super key;（mininal super key ）
   ![](https://techdifferences.com/wp-content/uploads/2016/12/Candidate-keys.jpg)

2. feature:
   1. unique;

3. used for？  
   行的标识符(row identifier)
   1. query row
   2. link data in other table(foregin key)

4. types of candidate key
   1. primary key: 被指定；primary

      ```
         PRIMARY KEY (`id`)
      ```

   2. alternate key: 未被指定为primary, 通常使用unique key标识

      ```
      UNIQUE KEY `ad_id` (`name`,`room`)
      ```

5. 实际中一个表有多个candidate key?
   yes, see blow:
   ![NEhuBo](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220430/NEhuBo.jpg)
6. prime attribute and non-prime attribute
   prime/non-prime attrubute: attribute(column) in/not in candidate key;

#### foreign key

1. what?
   在表里是候选键，在另一种不是主键， 是连接两个表的桥梁；
   1. a table: id,  s_id: child table
   2. b table, id  name: parent table

      ```
      # in a table;
      foregin key  s_id  reference b(id)
      ```

2. 通常使用主键作为外键

#### natural key vs sugrage key

1. what?
   使用哪个作为主键
   1. natural key:自然键，业务键，是业务里包含的字段(自然有的)作为主键；
   2. surrogate key: 代理键，额外新建的；

2. vs
   1. natural key:
      1. pros:
         1. 查询不需要额外join 操作；
      cons:
         1. 业务发生改变导致主键的重新选择:当前表重新选择主键；引用当前主键的表都要重新更新主键的值
            1. userTable: 社保号(primary key), usename, data,当用户没有社保号的时候，要重新选择主键，
   2. surrogate key:
      1. pros:
         1. 业务变化影响小
      2. cons:
         1. 占用额外空间
         2. 查询需要做额外的join 操作，因为下发给client 一般是业务标识
            user.order;
            user: userID, userID_forclient,  name,
            order:orderID,userID,cost

            select order.cost from order left join user where user.userID_forclient= xxxx;

## architecture

![c10jEI](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210924/c10jEI.jpg)

main part:

1. server part: generate  excute plan
2. engine part: manage data

sql  work:

1. connector:
1. create tcp connect
2. auth
2. parse (sql): ast tree
3. excute   plan
4. call engine

## store engine

what:
how to  manange data

compare:

1. transaction: innodb
2. row-level locker: innodb
3. left node:
1. innodb  store key and row value
2. myisam store  row pointer

      ![tj05Y0](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220501/tj05Y0.jpg)
      ![UOkvAG](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220501/UOkvAG.jpg)

### innodb

basic structure:

1. tablesapce: 存储table数据(leaf,non-leaf node),.idb file,
1. innodb_file_per_table: 每个table 一个 .idb
2. general tablespace: 多个table 共享一个 idb
2. segment: 一个space划分成多个segment;
3. extend: 64个page组成
4. page
5. row

  ```sql
  SELECT TABLESPACE_NAME, FILE_NAME FROM information_schema.FILES
  ```

   ![PxN2rK](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220510/PxN2rK.jpg)
   ![fBTY5l](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220516/fBTY5l.jpg)

 raw format:
   ![Js1Vn9](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220514/Js1Vn9.jpg)
   ![pRj1yt](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220514/pRj1yt.jpg)
  
1. nullable field bitmap

 off page (溢出页)
   [data pointer]->[offset page]
   if  record size <= innodb_page_size/2(8k);
   else:
      1. dymamic format: 20bytes(pointer) in-page, the remaining part off page

## index

what:
a datastructure to speed up retrive data at  the cost of extra space

### b+tree

![99Yktp](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20200505/99Yktp.jpg)

![ehvitl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210922/ehvitl.jpg)

b tree:   a  enhanced  version of avl, i/o oriented

1. more keys one node ,lower height

```c
internal node struct{
 keys [];
 values[];
 childNodes[]
}


leaf  node struct{
 keys [];
 values []
}
```

b+tree: a enhanced version of b tree

1. internal node only store key, more keys internal node, lower height
2. double linked list: speed up range query  

```c
internal node struct{
 keys [];
 childNodes[]
}


leaf  node struct{
 keys [];
 values []
}
```

### cluster index vs secondary index

clusater index:
leaf  node  store all data;

secondary index:
leaf node store key and primary key=

![geMubj](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20200504/geMubj.webp)

![6Gc9ph](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211214/6Gc9ph.jpg)

![4D9PTZ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211214/4D9PTZ.jpg)

### btree  

![vhw9V9](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210923/vhw9V9.png)

![yDYWBY](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210923/yDYWBY.png)

1. tree search 查找到left node;
2. 在left node 顺序查询；

### left most  prefix

 ![hy4WPe](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210922/hy4WPe.jpg)

what:  
a principle,  select from left to right following  composited index order

why:

composited key  大小  按照从左到右子key大小进行排序

example:

   ```c
   a char(3);
   b char(3);
   c char(3);
   KEY `index_abc` (a,b,c);
   ```

1. where a=1 and b=1: 命中,  keylength=6;
2. where a=1 and c =1 : 部分命中;keylength=3;
3. where b=1 and c=1: 未命中；
4. \> a， =b: 部分命中,keylength=3;

### fileSort

![YtcxzI](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211214/YtcxzI.jpg)

1. 是什么?
   一种排序算法；使用较小内存对大数据进行排序；
   1. 分段排序；
   2. 合并；
2. 触发场景: 未建立 索引字段进行排序；
3. 如何规避: 建立索引

## dataType

### 1. string

1. types:
   1. char,
   2. varchar
   3. text: text(2^16-1),mediumtext(2^24-1),longtext(2^32-1)
   4. blob:....

2. stroe in table: 可索引
   1. char: fixed; delete right space;
   2. varchar: variable and have extra 1-2byte to record size

3. varchar(255):
   1. <255:1byte + content;;
   2. >255: 2byte+content;

4. store out of table:   不可索引
   1. text: 按照指定方式编码
   2. blob: 没有编码

### numeric

1. int
   1. tinyint: 2^8-1
   2. int
   3. big int

2. float:
   1. float
   2. double;
   3. decimal(total_Digits, digits after deciaml point)

### 2 time

| Data Type | “Zero” Value          | other                   |
| :-------- | :-------------------- | ----------------------- |
| DATE      | '0000-00-00'          |
| TIME      | '00:00:00'            |
| DATETIME  | '0000-00-00 00:00:00' | 4byte, utc1970~utc2038; |
| TIMESTAMP | '0000-00-00 00:00:00' | 8byte,1000- 9999;       |
| YEAR      | 0000                  |

1. TIMESTAMP:会随着时区更新  store in utc, back from utc to the current time zone ;
   utc+8->utc-8;  查找: utc+8;
2. datetime: 在任何时区查找都获得那个时间；
   utc+8-> 不变; 查找: 不变

## normalization

what:
conforming  the norm form when desgin   database
why:

1. minimize redesign when extending the database structure(easy extend)
2. reduce anomaly: unexpected result
1. 删除异常:  want delete some filed but delete all field
3. 更新异常: updateing result in inconsistency state
4. 插入异常:  can't record when some field missed
   ![3zmVtl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220430/3zmVtl.png)

### how

how:

1. the key
2. the whole (candidate)key
3. only the (candidate)key;

#### the key

1. primary key,unique  identification(no duplicate row)
2. column(attribute) value are atomic

old:
| user_id | tags | age |
| ------- | --------- |----|
| 1   |  brave,lovely         | 18|

-------- update1 ---------

user_info  table:
| user_id | age |
| ------- | --- |
| 1       | 18    |

user_info_relation:
| relation_id | user_id | tag    |
| ----------- | ------- | ------ |
| 1           | 1       | brave  |
| 2           | 1       | lovely |

#### the whold key

non-prime attribute depend   the whole of every candidate key

origin:
| adID | third_id  | third_name |  is_used |
| ---- | --------- | ---------- | ------ |
| 1    | baidu_id1 | baidu001   | false  |

update
| ad_id | third_id  | is_used |
| ----- | --------- | ---- |
| 1     | baidu_id1 |      |

| third_id  | third_name |
| --------- | ---------- |
| baidu_id1 | baidu001           |

#### only the key

only depend the candiata key,  have no transitive key

origin:

| studentid | state | country | age |
| --------- | ----- | ------- | --- |
| 1         | cal   | usa     | 18    |

update:

student_info
| studentid | age | state |
| --------- | --- | ----- |
| 1         | 18  | cal   |
|           |     |       |

state_info
| state | country |
| ----- | ------- |
| cal   | usa        |

## transaction

what:
一组操作被当做一个不可分割的整体, all success or fail

configure:

   ```c
   set @@autocommit=1;
   xxxx;

   //the same as blow;
   set @@autocommit=0;
   begin;
   xxxx;;
   commit;

   ```

feature:

1. atomic: a logic unit, all success or fail;
2. consistency（correctness):  conform constraints
3. isolation: in concurrey condition, keep consistency   read (at same leve)
4. durability:   once commit, won't fail

### mvcc

what:
mutil-version concurrency control, 事务并发控制, read snapshot

current read :

1. select  for update(x lock);  lock in share mode(s lock)
2. update/insert/delete(x lock )

feature:

1. non-blocking
2. based on version number and snapshot

how it work:
check  row.version and trx.view.versionList

```c
```c
views  struct {
  minTrxVersion
  maxTrxVersion
  activeVersionList
}


if row.version <= view.maxTrx && row.id not in  view.activeVersionList:
 select row
else:
 row = row.previous 

```

   ![zFJkdP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220508/zFJkdP.jpg)

isolation(mvcc)  level:

1. read uncommited(dirty read): 无隔离
2. read commited: (non-repeated read)
1. not repeatable
2. phantom read
3. repeatable read
1. phantom read
4. serialization

read commited: read latest commited data
![esaOxf](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220503/esaOxf.jpg)

```c
create view at last 
init a active tx = 2 
T5:

 a.view.range=[2,3,5];
 a.view.activeList=[2,3,4];
 
 row.4 in activeList
 row.5 not in activeList


read row.5
```

repatable commited:事务期间提交的事务不会被读取
![d84lJv](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/d84lJv.jpg)

```c
create view  at first
init: a  active tx=2
T1:
 view.range=[2,3]
 view.activeList = [2, 3]


T5:
 row.version=5  > max range
 row.version=4  > max range
 row.version=1  < range

```

 serialization：
      1. what: implicitly convert all select to select...for share;

### phantom

what:  different result set   from some query

```c
table t1(id primary key)
  
t1: select t1 where id >1; // (1,+inf)区间未加锁，可以任意插入
// [2,3]
       t2: insert into id=4; commit;
        
       
t3:
 insert into 4; // duplicate key; 
 //= select id > 1: [2,3,4]; then throw error("duplicate key ")


 update set id=id+1  // affected row=3
 // = select id:[2,3,4], then +1;
 
```

why: 区间未加锁， 使得该区间可以任意被插入;  

why: 没有

   1. insert:new value
   3. delete
   4. update: 更改位置，=insert ord delete;

how:
update/delete/insert会自动加加锁；
select 手动加区间锁
`select...for update/share`

## log

types:

1. redo log
2. undo log
3. binary log

the log be record:
   ![D68VEj](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220516/D68VEj.jpg)

   1. 写入undo log, 更新data,
   2. 写入redo log and binlog
   3. success: flush redo log and binlog

### redo log

what:
the newest change of the record;  WAL(write ahead log)

content: the change of page

why need redo  log:  
improve  random write speed,

how:

1. update data
1. change in memory;
2. write  to redo log  cycle queue
2. commit
1. pop  queue,   write to  os buffer;
2. sync change  to   disk

config:
select @@innodb_flush_log_at_trx_commit
0. write and sync to disk every second

1. write and  sync to disk every trx, default
2. write to  os  buffer  every trx, sync to disk every second

![[Pasted image 20221123012043.png]]

   ![IhCrvP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220510/IhCrvP.jpg)

redo log buffer structure:

   ![3qlnNY](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210925/3qlnNY.jpg)

### undo log

what: latest snapshot  of a record;

for:

1. rollback
2. snapshot read

format: [log header,  field1,value1, field2,value2 ]

log header:

1. trxid
2. type
3. .....

![kizO0b](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/kizO0b.jpg)

context: history snapshot
   ![pTFael](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220516/pTFael.jpg)

rollback:  based log, inverse operation

### bin log

what:  the newest change of a log, operate log

for what:

1. sync slave
2. point-in-time recovery

content: logic

1. statement:  origin sql
2. row: the row of every record

```sql
|name | age|
 tang    10


update  table
where  @1=1
    @2='tang'
set 
  @1=1
 
```

3. mixed: both

bin vs redo log:

1. conent:
1. physical  log
2. logical log: sql
2. who:
1. redo: innodb
2. bin: server
3. use case:

config:
   select @@sync_binlog

   1. 0. only write to os buffer,
   2. 1. sync to disk every  1  trx
   3. n. sync to disk after n trx

point-in-time recovery

1. backup   data at some point

```sql
mysqldump -u root -p  > /path/a.sql

show master stauts #get  current position
```

2. replay from some backup postion to some postion

```
 mysqlbinlog   --start--posisitoin=154 --stop-position  >  /path/bak.sql
 mysql -u root -p < /path/bak.sql  
```

### 2 phase commit

what:

what:
确保数据一致的方法

phase:

1. request(prepare)
  write redo log to disk, redo log.stateu = prepare
  write bin log disk

2. commit()
 redolog.staute= commit;

2. 两阶段提交
   > binlog 与redo log数据一致性；
   redo log 从prepare 更新为 comit 过程；
   1. write redo log, statue = prepare;
   2. write binlog
   3. update redo log statue = commit;

   crash save:
      1. if redo log statue=commit, recover
      2. if redo log status= prepare, query log in bin log by trxID,if find, status= commit, recover;

## Locker

types:

1. table-level locks
1. table locks
2. intention locks  
3. row-level locker
1. record-lock:  lock a row, for updating row  concurrency
2. range lock:   lock a range, for  solving  phantom  read;
3. gap locker: (.... );
4. next-key locker:(....];
5. insert  intention lock (.....): 较少与其他插入的锁冲突

table locker:

```c
Lock  TABLES t1 READ[WRITE];
UNLock tables; 
```

table  intension lock:
a lock  before row-level lock, allow or forbid table lock;
协调表锁和行锁

2 phase:

   1. locker when update/delete/insert, or select xx for update/share mode
   2. unlock: commit;

### row-level  lock

types:

![PAlONR](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220621/PAlONR.jpg)

how row-level lock work:
secondary  index   and cluster index

   ![uvRWVC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220508/uvRWVC.jpg)

when  add range lock, curent read rules

1. unique index, select   where id=1
1. row exist: lcok record id=1
2. row not exist: (pre, after )
2. not unique  index,  select where age=1;
1. row exist:  (pre, 1], (1, after)
2. row not exist: (pre, after)
  
range example:

```sql
CREATE TABEL  gap (
 id INT PRIMARY KEY,
 num INT,
 KEY (`num`)
)
inert  into gap  values (1,1),(3,3),(5,5)
```

next key  list: (-inf,  1] , (1,3] , (3,5], (5,+inf)

1. `select * from gap wheree num=3 lock in share mode`:   (1 ,3] + (3,5)
2. `selct  * from gap gap where  id =4 lock in share mode`:   (3,5)

### dead lock

what: 互相持有对方需要的资源(锁)

how to solve:
before:

1. write  good  sql:
1. divide big tx into small
2. reduce   gap lock times
1. update exist data;
2. update by primary key

runniong:

1. out log

```
innodb_status_output=on
innodb_status_output_locks=on

```

#### case1

![[Pasted image 20221118221944.png]]

![p4Vtip](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/p4Vtip.jpg)

```
a: (20,30);
   b: (20,30)

a: try get (20,30)gap; 
       b: try get (20,30)gap;
    
```
  
#### case2

![[Pasted image 20221118224931.png]]

![[Pasted image 20221118224942.png]]

```
a:  (E,W],(W,+inf)
    
      b: (-inf,E],(E,W)

a: try get (E,W)gap,hold (w,+inf)
         b: try get (W,+inf)gap lock; hold (E,W)
    
```

## 优化

1. moniter
2. basic principle

### monitor

#### 1. slow log

1. variables:

   ```
   long_query_time
   slow_query_log  on/off
   log_queries_not_using_indexes on/off
   ```

### 1. explain

excution plan:

```sql
+----+-------------+-----------+------------+------+---------------+----------+---------+-------+------+----------+-------+
| id | select_type | table     | partitions | type | possible_keys | key      | key_len | ref   | rows | filtered | Extra |
+----+-------------+-----------+------------+------+---------------+----------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | employees | NULL       | ref  | name_idx      | name_idx | 4       | const |    1 |   100.00 | NULL  |
+----+-------------+-----------+------------+------+---------------+----------+---------+-------+------+----------+-------+
```

1. how:
   1. row: less
   2. type: use index

2. type
   1. 全量扫描
      ALL: 扫描主索引
      index: 扫描二级索引；

   2. 命中索引
      1. ref: 等值查询索引, =
      2. range: 范围查询索引，< , >
      3. const: 查询唯一索引或者主键；

3. select_type
   1. simple: 简单
   2. 复杂:
      1. subquery:包含子查询 subquery in select or where/having
      2. union: 包含联合查询

4. rows: 总扫描的行数

5. extra
   1. use index: covring index;
   2. 未使用所用index:
      1. order by, using filesort, extenal sort
      2. group by, Using temporary

### 索引优化

### 被索引到

1. 基于索引字段做查询:
   1. where: use index
   2. group by,order by:
2. 建立联合索引:
   1. use coving index: 经常查询字段加入联合索引,查询的时候只查询必要字段select(index1,index2) ;
      > explain  using index;
   2. 查询遵循left most prefix原则；

### 索引失效

1. 索引上做操作: 计算function;
      `where b+1 >0`
2. like: 前置通配符 "%tang";

3. !=  is [not] null, in(x,x,x), between 不会导致索引失效；
   mysql会预估

### null

1. pros:
   1. null值不占用空间

2. cons:
   1. 基于 null的运算失效
      1. compare: is null, is not null
      2. operation: null +/-/x/%// number = null
      3. aggregation: count;
      4. 不包含在剩余子集里: c !=1;--> c != 1 or c is null;

3. other:
   1. 会使用索引
   2.

## 读写分离

1. what?
   读写分离，主从同步
   读和写在不同的 主机上；

### replicate

1. replicate;
   ![29QH9V](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220514/29QH9V.jpg)
   slave 通过 master的 binlog

2. how to set up;
   1. master::
      1. grant privilege to slave;

         ```
         GRANT REPLICATION SLAVE ON *.* TO 'replica_user'@'replica_server_ip'
         ```

   2. slave: change master source;

      ```
      mysql2 > CHANGE master  TO
      SOURCE_HOST='source_server_ip',
      SOURCE_USER='replica_user',
      SOURCE_PASSWORD='password',
      SOURCE_LOG_FILE='mysql-bin.000001',
      SOURCE_LOG_POS=899;
      ```

   3. mysql2 >

3. level:
    1. async;
    2. 半同步: 至少一个slave 写入relay log 成功
    3. 同步

4. 主备切换
   1. 手动切换

### load balance

1. two:
   1. proxy
   2. code:
      if action=query:
         do in slave db;
      else
         do in write master db

2. 数据一致性: 无法做到强一致性
1. 强制读master
      1. 已更新数据 标记为dirty;
      2. if data is in dirty, read master;

#### proxy

1. mysql-proxy

   ```
   proxy-read-only-backend-addresses=192.168.73.131
   proxy-backend-addresses=192.168.73.130

   ```

2. mycat

## 分库分表

1. what?
   将数据拆分到多个库，表中，减轻压力；

2. why:
   1. 分库:  减轻主机(node)压力
   2. 分表: 减轻表的压力

### table split

1. 水平 vs 垂直
   1. 水平:行的维度拆分
   2. 垂直:列的维度拆分

2. partition
   1. range
   2. list
   3. hash

3. case

   ```
   create table data (
    id integer primary key, 
    status char(1), 
    data1 varchar2(10), 
    data2 varchar2(10) )
    partition by list (status) ( 
       partition active_data values ( 'A' ),
       partition other_data values(default) 
    );
    ```

#### vertical

1. 将不常用字段拆分

   ```
   create table data (
    id integer primary key, 
    status char(1) not null, 
    data1 varchar2(10) not null, 
    data2 varchar2(10) not null);

   ```

   ```
   create table data_main (
    id integer primary key,
    status char(1) not null,
    data1 varchar2(10) not null );

   create table data_rarely_used (
       id integer primary key,
       data2 varchar2(10) not null,
       foreign key (id) references data_main (id) );

   ```

![[Pasted image 20221118183859.png]]

![[Pasted image 20221118205755.png]]
i am very sad to see you when i

good


## mysql dump 


how to dump:

1.  get souce from origin :

```
mysqldump -u root -p source_db source_table > dump_file.sql
```


2. dump into current 

```
mysql -u root -p < dump_file.sql
```





## DSN

loc:  determine   the time.Time's string  when insert into  database; 

when set to  loc = beijing;

insert: convert time to beijing locaiton time string, then insert  it 
select:  Interpret the time string from the database as being in Beijing's time zone.





## null in sql  

the cons   of use null
性能开销
1. 增加存储空间,需要使用 额外一个bit 记录是否为null，但是使用非null默认值如0, "",占用空间反而更大
2. 索引失效: 并不会使得索引失效

使用开销: 开发者需要额外使用 is null, not null 判断有null 的column

the pros :
在表示一些 确实没有 的数据， 如邮箱， 号码， 直接使用null 会比使用默认值更容易理解；


结论:
如果业务中确实存在一些



## decimal 


what's decimal;

how:


##  

