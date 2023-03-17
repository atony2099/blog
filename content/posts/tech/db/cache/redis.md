---
title: redis and  cache
date: "2021-02-20T16:03:56+0800"
draft: false
tags: ["redis","hash","database","locker","reactor"]
---
[Redis 系列（二）: 连集合底层实现原理都不知道，你敢说 Redis 用的很溜？](https://xie.infoq.cn/article/98c984f6462aec99ffc0c3b42)

[压缩列表](https://redisbook.readthedocs.io/en/latest/compress-datastruct/ziplist.html)

[数据库](https://redisbook.readthedocs.io/en/latest/internal/db.html#id2)

[Redis(2)——跳跃表](https://segmentfault.com/a/1190000022028505)

[缓存系统稳定性 - 架构师峰会演讲实录](https://zhuanlan.zhihu.com/p/371039849)

[Redis专题：万字长文详解持久化原理
](https://segmentfault.com/a/1190000039208726)

[Bloom Filters - the math](https://pages.cs.wisc.edu/~cao/papers/summary-cache/node8.html)

[All About Bloom Filters](https://freecontent.manning.com/all-about-bloom-filters/)

[Redis 6.0 新特性：带你 100% 掌握多线程模型](https://segmentfault.com/a/1190000040376111)

[Redis进阶 - 高可用：哨兵机制（Redis Sentinel）详解](https://pdai.tech/md/db/nosql-redis/db-redis-x-sentinel.html)

[Redis Sentinel-深入浅出原理和实战](https://www.51cto.com/article/634230.html)

## high performace



3. 多线程?     
    1. 提高数据处理速度: read,decode,write,
    2. 折中的master-worker,总体看还是单线程
        ![3wuNaR](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220715/3wuNaR.jpg)



## mechanism 

###  basic  structure
   
   ![bAr65d](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220519/bAr65d.jpg)
dbList: bList:[redisDB1,redisDB2,...]

store form:
1. value dict: dict[stringRedisobj] =  some redisObj
2. expire dict: dict[strinngRedisObj] = expire time 


reids  object:
econding: underlying implement
```c
typedef struct redisObject {
   // 类型
   unsigned type:4;
   // 对齐位
   unsigned notused:2;
   // 编码方式
   unsigned encoding:4;

   // LRU 时间（相对于 server.lruclock）
   unsigned lru:22;

   // 引用计数
   int refcount;
   // 指向对象的值
   void *ptr;

   } robj;

# types:
#define REDIS_STRING 0  // 字符串
#define REDIS_LIST 1    // 列表
#define REDIS_SET 2     // 集合
#define REDIS_ZSET 3    // 有序集
#define REDIS_HASH 4    // 哈希表


# encoding:

#define REDIS_ENCODING_RAW 0            // 编码为字符串
#define REDIS_ENCODING_INT 1            // 编码为整数
#define REDIS_ENCODING_HT 2             // 编码为哈希表
#define REDIS_ENCODING_ZIPMAP 3         // 编码为 zipmap
#define REDIS_ENCODING_LINKEDLIST 4     // 编码为双端链表
#define REDIS_ENCODING_ZIPLIST 5        // 编码为压缩列表
#define REDIS_ENCODING_INTSET 6         // 编码为整数集合
#define REDIS_ENCODING_SKIPLIST 7       // 编码为跳跃表

```

###  high performace 

why have high performace:
1. 多路复用（select or epoll: 高效处理tcp连接；
3.  High performace DataStruct;
4.  in-memory store ;
5. single thread:no locker cost 



###  mutiple thread


why single thread: 
not cpu-bound, i/o bound and memory  bound

but why  use mutiple thread now:
improve i/o, simple version of multi reactor;
child thread work:  read/write socket ;



### zipList 

1. what？
    动态数组
2. feauture:
    1. 元素大小可变，节省空间；

```
area        |<---- ziplist header ---->|<----------- entries ------------->|<-end->|

size          4 bytes  4 bytes  2 bytes    ?        ?        ?        ?     1 byte
            +---------+--------+-------+--------+--------+--------+--------+-------+
component   | zlbytes | zltail | zllen | entry1 | entry2 |  ...   | entryN | zlend |
            +---------+--------+-------+--------+--------+--------+--------+-------+
                                       ^                          ^        ^
address                                |                          |        |
                                ZIPLIST_ENTRY_HEAD                |   ZIPLIST_ENTRY_END
                                                                  |
                                                         ZIPLIST_ENTRY_TAIL
                                                         
                                                         
area      |<---------------------- entry ----------------------->|

size        ?                  2 bit      6 bit    11 byte
          +------------------+----------+--------+---------------+
component | pre_entry_length | encoding | length | content       |
          |                  |          |        |               |
value     | ?                |    00    | 001011 | hello world   |
          +------------------+----------+--------+---------------+
```


| 域      | 长度/类型 | 域的值                                                                                                                                                                  |
| :------ | :-------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| zlbytes | uint32_t  | 整个 ziplist 占用的内存字节数，对 ziplist 进行内存重分配，或者计算末端时使用。                                                                                          |
| zltail  | uint32_t  | 到达 ziplist 表尾节点的偏移量。 通过这个偏移量，可以在不遍历整个 ziplist 的前提下，弹出表尾节点。                                                                       |
| zllen   | uint16_t  | ziplist 中节点的数量。 当这个值小于 UINT16_MAX （65535）时，这个值就是 ziplist 中节点的数量； 当这个值等于 UINT16_MAX 时，节点的数量需要遍历整个 ziplist 才能计算得出。 |
| entryX  | ?         | ziplist 所保存的节点，各个节点的长度根据内容而定。                                                                                                                      |
| zlend   | uint8_t   | 255 的二进制值 1111 1111 （UINT8_MAX） ，用于标记 ziplist 的末端。                                                                                                      |


### quickList
 ![d27rcR](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220519/d27rcR.jpg)
   


vs linker: reduce alloc time
vs array: reduce moving cost

###  skipList

![1X8WAl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210907/1X8WAl.jpg)


what:
order linker list  with  mutiple  next  pointer 

simple code:
```c
struct quickListNode{
	[]nextNoeList

	....
	key 
	value  
}


search: currNode, key
	for _, node := range  nextNodelist:
		if key node v.key:
			currNode=node, search(currNode,key)

		if key == node.key:
			retunr node

	return  notFound;
		
	

```


 ```c
typedef struct zskiplistNode {
          // member 对象
          robj *obj;

          // 分值
          double score;

          // 后退指针
          struct zskiplistNode *backward;

          // 层
          struct zskiplistLevel {

              // 前进指针
              struct zskiplistNode *forward;

              // 这个层跨越的节点数量
              unsigned int span;

          } level[];

} zskiplistNode;


typedef struct zskiplist {
	  // 跳跃表头指针
	  struct zskiplistNode *header, *tail;
	  // 表中节点的数量
	  unsigned long length;
	  // 表中层数最大的节点的层数
	  int level;
} zskiplist;

```


3. vs avl tree;
    1. 范围查找更方便
    2. 更简单: 不需要为了平衡而进行旋转操作



## Data  Types

![oKDz0J](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211018/oKDz0J.jpg)


###  string
sds,simple dymanic sting:
```c 
  struct sdshdr {
	  long len;
	  long free;
	  char buf[];
  };
  char greeting[6] = {'H', 'e', 'l', 'l', 'o', '\0'};
```

sds feature:
1. 二进制安全: c会被\0截断； 而 sds通过len能获取完整长度；
2. 高效append: 通过预先分配free空间;减少ReAlloc次数;

sds code:
   ```c
   def sdsMakeRoomFor(sdshdr, required_len):

       # 预分配空间足够，无须再进行空间分配
       if (sdshdr.free >= required_len):
           return sdshdr

       # 计算新字符串的总长度
       newlen = sdshdr.len + required_len

       # 如果新字符串的总长度小于 SDS_MAX_PREALLOC
       # 那么为字符串分配 2 倍于所需长度的空间
       # 否则就分配所需长度加上 SDS_MAX_PREALLOC 数量的空间
       if newlen < SDS_MAX_PREALLOC:
           newlen *= 2
       else:
           newlen += SDS_MAX_PREALLOC

       # 分配内存
       newsh = zrelloc(sdshdr, sizeof(struct sdshdr)+newlen+1)

       # 更新 free 属性
       newsh.free = newlen - sdshdr.len
       # 返回
       return newsh

   ```


###  list



how:  quickList

use case: 
store data sorted by added time

```shell  
LPUSH  u01  water 
LPUSH  u01  milk
LPUSH  u01  tea

# [ tea,milk,water] 
# left index:[0,-1];  right index:[-1, -3]

LRANG  u01  0 -1;# getall item from left to right(new to oldest)

LRNAGE a -3 -2s; # get  earliest two item
```

### hash  

structure :
`reidsKey field1 value1, field2 value2 ....`

use case:
1. record some item info 
```c
hset tang age 18;
hset tang id 01;
```

2. increase field count
```
hincrby pv home 1;
hincrby pv detail 1;
```

implement:
1. zipList
2. hash


### set

use case:
1. remove duplicated things

```c
SADD visitip 1.2 1,3;;
SMEMEBERS visitip # 
SCARD visitip # 
```

how it work:
1. hash
2. 整数集合 

###  zset

what:
sort set
also act  as  a simple table:
```
id(primary key)  score(int)
		xxxx         xxx
```

use case:

1. rank
 `select from rank order by  score asc limit 3 offset 3`
 `update set score=score+n where id=xxx`
```c
sadd rank  100 u1 200 u2 300 u3
zincrby rank 1 u1

zrangebyscore rank -inf +inf limit 0 3;
```

2. count number in  score range
if score=timestamp, slide window 
`select count(*) from table where score>=a and score<=b`
```c
sadd u1  2022030401 randomID
sadd u1  2022030401  randomID
scount ui 2022030401 2022030401
```

  
 how it:
1. zipList
2. skipList

##  cache problem

what:
1. cache not work
2. big pressure on db



types:
1. all:  cache avalanche
2. partly:
        1. 击穿(hotspot invalid): hot keys(one or some) invalid;
        2. 渗透(cache penetration): same keys always query from db,


how:
1. cache avalanche:
	1. before: high availablity: master-slave or clusrter architectue
	2. in progress: 降级，限流
2. hotspot invalid: 
	1. expired time:
	   1. don't have expired time: configure data;
	   2. set  different expired time;
	2. add Locker  when access hot key;
2. cache penetration:
	1. valid data;
	2. bitMap or blomfilter to cache existing data;




## cache  consistency

what:  cache data and mysql data not consitent

optianl  solution:
1. update cache, then update db: 🚫
     if update db fail, must retry
2. update  db, then update cache: 🚫
```c
Processs A               Process B
updateDB name=a            
					updateDB name=b
					updateCache name=b;
updateCache name= a  


 
-------
// cache is nil 
select a                db: a=1; cacahe: a=nil
		update db a     db: a=2; cache: a=nil
		delete cache a  
set cache a            cache=1
			
```


best solutiion:
update mysql, then delete cache










why:
update db then update cache: will be overwrte by older process
```c
   
```


## write behind;




## cache  strategy;
1. cache aside(lazy loading):
    read: client-> cache->db;
    update: client->db,  client->cache[delete];

2. read/write through: 由代理管理数据库和cache;
    client -> proxy->{cache,db}

3. write behind caching;    
    client -> cache; then after a time:  cache -> write db;

    strategy: 
    1. interval:  after x sconds; 
    2. lazy: after updating;  


   problem: 一致性降低；
    策略:
    1. 提高cache 稳定性；
    2. 只从cache读取；


## bigData


1. 基于位的存储
   1. bitmap
   2. blomfilter:

2. 基于概率的存储
    1. hyperloglog

### bitMap((bitArray)

1. what?
    array of bit 
    1. int value = array  index;
    2. bitarray[arrayIndex]=1


2. feature？
    1. 适合存储分布均匀的整数；
    2. 能还原实际值；


2. use case?
    1. userID is autoIncrement


#### roaring bitmap
1. what? bitmap 改进版，在存储少量数据的时候节省空间
2. how? 
   空间进行分割: 
    [1]:int16 arrary or bitArray
    [.]:
    [.]
    [2^16]


###  bloomfilter
1. what?
    bitmap(array)的应用
    1. index = hashCode%bitLength
    2. store multiple index;

2. how？
    value1-> hashValue1%bitarrayLength=bitArrayIndex1;
    value1->hashValue2%bitarrayLength=bitArrayIndex2;
    if bitArray[bitArrayIndex1]=true && bitArray[bitArrayIndex2]=true; data exist 

    ![BicQ1J](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220614/BicQ1J.jpg)



3. 占用空间？2^32数字占用空间
    ![goU1ih](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220614/goU1ih.jpg)



    when k=4; 
    m/n(totalBit/totalEle)= 10~20
    false positive:  0.01 ~0.001
    

4. use case
 

### hyperloglog


## persistance data

types
aof:append only file; command  list  
rdb: redis database;  snapshot of memory 



recover:
if  aof enable, load aof file frstly


### aof 


config:
```c
appendonly yes
appendfsync everysec
appendfilename appendonly.aof  
```
appendfsync: when flush to disk
1. everysec
2. always:  every  write command 
3. no:  controls by  os;


how:
```
write  to buffer;
write to  os cache

if some condition:
	flush to  disk 
```

      ![sAalHM](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211212/sAalHM.jpg)

pros: less data loss;
cons: big file and more recover tim


#####  rewrite aof 

what: 
merge the commands;

config:
```c
// the condition of rewrite
auto-aof-rewrite-percentage 100 
auto-aof-rewrite-min-size 64mb  
```


 how:
```
childProcess=fork():
	rewrite to aof file  or rdb
	append the increase command


```

    ![NDCDgZ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211212/NDCDgZ.jpg)
![[Pasted image 20221115052856.png]]



### RDB

config: 
```c
save 900 1
save 300 10
save 60 10000
```
in  60s, 900 keys have changed 

how:

```
bgsave:
	childProcess=fork()
```

     ![s5mDwy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211211/s5mDwy.jpg)



pros: 
1.  compact and less recover time
cons:  
1. more  data  loss 



##  eviction  and   expire


eviction:
evict data when memory reach the max memory 
```c 
maxmemory-policy   noeviction 
```

eviction types:
1. noeviction: default
2.  volatile data
	1. volatile-random:
	2. volatile-ttl
	3. volatile-lru
	4. volatile-lfu
3. all keys
	1. allkeys-random
	2. allkey-lru
	3. allkey-lfu

expires:
remove expired keys


how: lazy-expire and auto 
```
get(key):
	if ttl<0, del key


cron: every n  seconds:
	get random 20 keys;
	check ttl and del
	if  ttl<0's key > n and excute time < n ms: rerun again

```


## replica




replication:  手动故障转移 

```c 
// master 
REPLICAOF  no one  

// slave1
replicaof 192.168.1.171 6379

// slave2
replicaof  192.168.1.171 6379

```





sentinel: automic failover

```c
port 26379
sentinel monitor mymaster 192.168.0.60 6379 2
```

![sM1u7X](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/sM1u7X.jpg)

![j9t9WY](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/j9t9WY.jpg)
![xg11vQ](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/xg11vQ.jpg)
how: 
1. send heartbeat to check health
2. heartbeat fail:  
	1. client send  sentinel is-master-down-by-addr:  get vote >  quorum, 客观下线
	2.  client send  sentinel is-master-down-by-addr: get vote > quorum， 成为leader 
3. leader  do failover