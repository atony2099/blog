---
title: Kafka
date: "2020-12-07T17:54:16+0800"
draft: false
tags: ["kafka","mq","io"]
---

[Chapter 4. Kafka Consumers: Reading Data from Kafka](https://www.oreilly.com/library/view/kafka-the-definitive/9781491936153/ch04.html)

[Kafka Rebalance机制分析](https://www.pianshen.com/article/33431076149/)

[Deep Dive Into Apache Kafka | Storage Internals](https://rohithsankepally.github.io/Kafka-Storage-Internals/)

[How Kafka’s Storage Internals Work](https://thehoard.blog/how-kafkas-storage-internals-work-3a29b02e026)

[Kafka消息时间戳(kafka message timestamp)](https://www.cnblogs.com/huxi2b/p/6050778.html)

[A Practical Introduction to Kafka Storage Internals](https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f)

[原来 8 张图，就可以搞懂「零拷贝」了 ](https://www.cnblogs.com/xiaolincoding/p/13719610.html)


##  architecture

![HFRZk1](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210127/HFRZk1.jpg)
 ![tocPUC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201207/tocPUC.jpg)
![[Pasted image 20221208220120.png]]
![FjJyb0](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201215/FjJyb0.jpg)

1. producer
2. consumer group
3. broker:  kafka 集群节点
4.  topic:  一组消息的集合
	1. partition:   subset  of topic,locate in different broker
	2. segement:  subset of  partition

5. zookeeper: meta data
	1. brokerList
	2. topList 
	3. consumer
		1. consumer list
		2. consumer offset


##  replicas

what:
一份数据多个备份






## quick  
why:
1. 顺序写入;
2. zero coy 
3. partition: 并发写入/读取




  

## message

![zOZ1oT](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/zOZ1oT.jpg)

![](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2015/355c1d57.png)



##  produce 



###  partition  

1. partition 
1. key  is  null: round-robin 
2. key, hash(key) mod partitionCount









## consumer


1. how 
    partitions: [partitin0, partition1,partition];
    consumers:  [consumer1, consumer2,consumer2];

    one consumer to  [one partiion OR many partitin]


    1. case 1:
        [p1-p4:c1-c2] c1:p1,p2 c2:p3,p4;

    2. case 2:
        [p1-p4,c1-c6] c1:p1...c4:p4, （c 5,c6 are idle);

2. stratetgy
   1. rangeAssignor 
        distribute over partions by order; 
        ![PWI910](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211120/PWI910.jpg)

   2. roundRoubin 
        evenly distribute
        ![ovmJHD](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211120/ovmJHD.jpg)

    


### 1. consumer rebalance
再平衡机制，确保分区能尽可能均匀分配到消费者上
触发条件

1. consumer group
2. 订阅topic变化
3. 订阅topic分区数变化

### 2. PartitionAssignor Strategies

分配给消费者的策略

1. RangeAssignor
2. RoundRobinAssignor


### 3.auto.offset.reset

a new consumer group have not have s stored  offset in kafka;

1. earliest: automatically reset the offset to the earliest offset
2. latest: automatically reset the offset to the latest offset


## config

### produce config

acks:
   0:不等待任何ack
   1:等待leader ack
   all: 等待所有ack

### broker config

1. log.message.timestamp.type

Define whether the timestamp in the message is message create time or log append time. The value should be either `CreateTime` or `LogAppendTime`

|         Type: | string                      |
| ------------: | --------------------------- |
|      Default: | CreateTime                  |
| Valid Values: | [CreateTime, LogAppendTime] |
|   Importance: | medium                      |
|  Update Mode: | cluster-wide                |


## how kafka retrive message

1. 通过offset二分查找对应的segment;

2. ![Z2lmOK](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201215/Z2lmOK.jpg)

   ![image](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2015/c415ed42.png)

1. 在index 文件上查找对应的relativeOffset; =  offset-baseoffse(文件名)+1;

     > 并没有为每个offset都建立索引；只能先查找与它最接近的relative； 再顺序扫描log文件

2. 找到对应relative offset 对应的position; 再在log文件中查找

## timeStamp

引入时间戳主要解决3个问题：

- 日志保存(log retention)策略：Kafka目前会定期删除过期日志(log.retention.hours，默认是7天)。判断的依据就是比较日志段文件(log segment file)的最新修改时间(last modification time)。倘若最近一次修改发生于7天前，那么就会视该日志段文件为过期日志，执行清除操作。但如果topic的某个分区曾经发生过分区副本的重分配(replica reassigment)，那么就有可能会在一个新的broker上创建日志段文件，并把该文件的最新修改时间设置为最新时间，这样设定的清除策略就无法执行了，尽管该日志段中的数据其实已经满足可以被清除的条件了。
- 日志切分(log rolling)策略：与日志保存是一样的道理。当前日志段文件会根据规则对当前日志进行切分——即，创建一个新的日志段文件，并设置其为当前激活(active)日志段。其中有一条规则就是基于时间的(log.roll.hours，默认是7天)，即当前日志段文件的最新一次修改发生于7天前的话，就创建一个新的日志段文件，并设置为active日志段。所以，它也有同样的问题，即最近修改时间不是固定的，一旦发生分区副本重分配，该值就会发生变更，导致日志无法执行切分。（注意：log.retention.hours及其家族与log.rolling.hours及其家族不会冲突的，因为Kafka不会清除当前激活日志段文件）
- 流式处理(Kafka streaming)：流式处理中需要用到消息的时间戳

![](https://images2015.cnblogs.com/blog/735367/201611/735367-20161110142047577-1208698220.png)



## mq problem
### order consume

1. 全局有序
```
topic create ---partitions 1

```
2. 局部有序: 同一个partition下有序
	1. same key 
	2. 



### avoid lost 

![kH9Yn0](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/kH9Yn0.jpg)

ack:
1.  producer  ack:  
	1. 0: no  response
	2. 1: leader response
	3. all: all  replica response 
```
producer.ack = all

```


2. topic：set  min.insync.replicas

```c
bin/kafka-topics --bootstrap-server localhost:9092 --create --topic my_topic_name \
 --partitions 20 --replication-factor 3

/usr/bin/kafka-topics --alter --zookeeper zk01.example.com:2181 --topic topicname --config min.insync.replicas=2
```


3. consumer: ack  
```c
auto_commit=false
auto_commit_interval=1s

```


### duplicate 

Idempotence:
excute mutiple time and  get same  result , `f(f(x)) = f(x)`


when:
1. producer  retries 
2. consumer  retries:  fail to  commit 

how:

1.  unique key: insert  
2.  global   id:
```
if get (mssage.id) != null{
	
	return 
}

handle(message);
set(message.id)

```
   

