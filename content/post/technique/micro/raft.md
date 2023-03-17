---
title: distributed system(computing)
date: "2021-02-14T10:37:33+0800"
draft: false
tags: ["raft"]
---

[持续可用cap](http://blog.sina.cn/dpool/blog/s/blog_e59371cc0102vk87.html?md=gd&wm=3049_b111)

[What is the CAP theorem?](https://debugged.it/blog/cap-theorem/)
[CAP Theorem](https://www.ibm.com/cloud/learn/cap-theorem)

[资深程序员实践经验：大白话讲解“什么是CAP定理？”](https://zhuanlan.zhihu.com/p/75462177)

[What is the CAP Theorem? MongoDB vs Cassandra vs RDBMS, where do they stand in the CAP theorem?](https://medium.com/@bikas.katwal10/mongodb-vs-cassandra-vs-rdbms-where-do-they-stand-in-the-cap-theorem-1bae779a7a15)

[00 开篇词 吃透分布式数据库，提升职场竞争力](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/24%E8%AE%B2%E5%90%83%E9%80%8F%E5%88%86%E5%B8%83%E5%BC%8F%E6%95%B0%E6%8D%AE%E5%BA%93-%E5%AE%8C/)

[https://draveness.me/consensus/](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.67.6951&rep=rep1&type=pdf)

[共识、线性一致性与顺序一致性](https://segmentfault.com/a/1190000022248118)

[分布式系统中的Linearizability一致性的概念介绍](https://donggeitnote.com/2021/06/27/linearizability/)

[「分布式计算」什么是严格一致性和最终一致性？](https://cloud.tencent.com/developer/article/1769521)

[「图解Raft」让一致性算法变得更简单](https://zinglix.xyz/2020/06/25/raft/)

[Raft算法详解](https://zhuanlan.zhihu.com/p/32052223)

[ETCD背后的Raft一致性算法原理](https://www.jianshu.com/p/5aed73b288f7)

[一文搞懂Raft算法](https://www.cnblogs.com/xybaby/p/10124083.html)

[Raft 协议实战系列（六）—— 线性一致性与读性能优化](https://juejin.cn/post/6906508138124574728#heading-7)

[ Introduction to the Raft algorithm](https://www.sofastack.tech/en/projects/sofa-jraft/raft-introduction/)

[The Raft Consensus Algorithm](https://web.stanford.edu/~ouster/cgi-bin/cs190-winter20/lecture.php?topic=raft)

[ etcd教程(七)---读请求执行流程分析](https://www.lixueduan.com/post/etcd/07-read-process/)

[Strong   consistency models](https://aphyr.com/posts/313-strong-consistency-models#sequential-consistency)

[Consistency Models](https://jepsen.io/consistency)

[分布式系统中的一致性模型](https://blog.csdn.net/qq_29328443/article/details/118423602)``

[ CAP Theorem for Databases: Consistency, Availability & Partition Tolerance](https://www.bmc.com/blogs/cap-theorem/)

[raft paper](https://raft.github.io/raft.pdf)

[ Making sense of the RAFT Distributed Consensus Algorithm — Part 1](https://codeburst.io/making-sense-of-the-raft-distributed-consensus-algorithm-part-2-4f12057b019a)

[ Making sense of the RAFT Distributed Consensus Algorithm — Part 2](https://codeburst.io/making-sense-of-the-raft-distributed-consensus-algorithm-part-2-4f12057b019a)

[看动画轻松学会 Raft 算法](https://juejin.cn/post/7101886330292371463)

[ 强一致性、顺序一致性、弱一致性和共识](https://blog.nowcoder.net/n/bd9041ddc8314dd9946957dd9999d4de?from=nowcoder_improve)

[ 第01课：分布式系统的问题](https://gitbook.cn/gitchat/column/5b444ae694c0f60b4ec4a68c/topic/5b444ecf94c0f60b4ec4a899)

## basic idea

global clock in distributed system :  
1. 物理:  ntp 同步 多个node, 使得各个node有一样的时间
	>   impossible: 网络延迟
2. 逻辑:  全局顺序一致 
> 


##  what 
  
![kPSu08](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210214/kPSu08.jpg)


what: 
use mutiple computing node to achieve some goal 

advantages:
1. high performace:
2. reliability, fault tolerant: continue work  in the presence of some faulty processes

how to implement:
1. share   algorithm
2. replication algorithm
	1. central:
		1. master-slave
	2. decentral: consensus algorithm
		1. raft


## sharing

algorithm types:
1. hash
2. range: [1~100]


## replication protocol

primary/secondary:
	![UEp5wD](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/UEp5wD.jpg)


state-machine: 
every node  have same initial state, same inputs in the same  order  will arrive at the same output 
```c 
node1: [input1,input2,...] ->  transitionF ->[output1,output2,....]

node1: [input1,input2,...] ->  transitionF - [output2, output2,...]

```

![hZWR26](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/hZWR26.jpg)



## consitency  model 

![rSZV5c](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220706/rSZV5c.png)

what's a model:

读取结果满足特定的排列组合，一致性越低组合越多

for example
```
process1  w(a,1)----w(a,10)----
process2      w(x,2)


processs3:-------------------------- read(a), read(a),read(a)


reesult set: [1,2,10],[1,10,2]
```


strong: 
1.  线性一致性(atomic,Linearizability consistency):
2.  顺序一致性(sequential )
3. 因果一致性

weak feature:
1.  最终一致性(eventual consistency ): 最终达到一致

weak  case:
1.   mysql主备

Linearizability feature :
1.  always read the newest write
2.  与全局时钟视角下一致






Linearizability  case:
1. etcd 


   ![aN2MLV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211210/aN2MLV.jpg)

2. types?
   1. 

      1. recent: a read must return the most recent value written;
   
      2. the same order:
         1. all processor see the same order, equivalent to the global time ordering



   2. 顺序一致性:
         1. the same order:


   3. 因果一致性:
      1. 
   4. 最终一致性：


3. strict consistency: 总是返回最新值；
   1. 主节点先复制，再返会结果->性能降低
5. eventual consistency:最终返回最新值； 
   1. 主节点先返回，再复制->性能高


## cap 

cap
1. consisency: return the same data: recent data or error
2. availability:  all node return  data 
3. partition tolerance: network error-> divide a network into mutiple part -> system continue work 

 meaning: 
```
in network error

current node:
	return current value;  availability 
	return error;  consistency
```

meaningful:   可用性和一致性存在反相关
1.  high consistency,less availability 
2. high availability,less consistency



## Raft  algorithm

what:
1. consensus  algorithm
2.  one of algorithm to implement state machine replication 
3. 

 core:
   1. leader election;
   2. log replication;
   3. safety

###  leader election

the  key:
1. get marjory votes
2. vote when the candidate have newest log

code:
```c
type Nodde struct {
	state  int //
	votedForCandidate string 
}

notReceiveHeartbeat > n ms
	node.state= candidate
	node.term++
	result  = requestVotes
	if get majority: be leader
	if timeoout:  keep candidate,  requestVote again
	if receive newheartbeat:  be  follower 





requestVote: cTerm, cLastLogTerm,c LastLogIndex
	if cTerm>=myTerm: 
	   cLog is up-to-date:
		  cTerm >= myterm ||  cLastLogTerm==myterm && cIndeex
	else
		return false

```

![2](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211210/2.gif)
![7eaNBI](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211227/7eaNBI.jpg)

![KfR3pi](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211226/KfR3pi.jpg)



###  log replication 

feature
1. 预写日志
2. 

####  how

the process:

1. vote: write log   
2. commit:   leader  apply   

write  log

```c
type log struct {
	index int 
	term  int 
	command  interface
}



type Node {

	appliedIndex int
	committedIndex int 

}


vote:
	getVote

if receive half votes: be  committed state,commitedIndex++
		new thread:  
			apply state machine, applied++
			notify other node to apply
		sendClient ok

	
getvote:
	appendLog
```

![5n2vNl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211209/5n2vNl.jpg)

![bghtpe](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211210/bghtpe.jpg)

![r6OW73](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220102/r6OW73.jpg)

![kDcoTa](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211230/kDcoTa.jpg)

   ![wOzU9z](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211227/wOzU9z.jpg)


###  safety
what:  一些属性(约束)以保证算法最终正确

list:
1. leader: 
	1. one leader: 最多选举出一个leader
	2. completeness:   leader have all commitedIndex
	3. leader only can  append
2. log match:   if log.term and log.index is same, all previous logs are identical; 
3. state machine safety: 同一个 index不能被apply不同的日志；


#### log match

log match：leader 通过比较，删除不符合的日志，补充缺少的日志
if leader.log[index].term !=  node.log[index].term
	 delete node.log[index],  apend leader.log[index]

![Zj1Qjy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220414/Zj1Qjy.jpg)


sate machine safety:
leader不能commit 之前任期的日志；

   ![oSnqve](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220413/oSnqve.png)

```
in c:  s1  sync  log 2  to s3, and commit 2
in d:  overwrite 2


in c: s1 sync log 2 to s3, and not committed
in d: overwrite 2,  we  can take the risk 

```

##  read

problem:

1. applied  index  < committed index
2.  split brain 



how:
1.  转发给leader 
2. wait appliedindex>=committedIndex
3. avoding  split: 
	1. read log  replication
	2.  send heartbeat,  chek if  mystatus== leader 
	3.  if  currentTime-lastCheckTime   < reElect time, not  heartbeat check, else check;



1. read what?
   read commitIndex;
   1. get commited index;
   2. get value: when applyIndex >= commitedIndex    

2. get the commiteIndex:
   1. who: leader;
   2. how: 
      1. send heartbeart to ask for vote;
      2. result:
         1. majority votes: is leader, send commited index 
         2. less half: not leader, send error




##  脑裂(brain split)


what:
network error-> partition -> mutiple leader   



how:  多数者投票机制


case1: 
老leader 所在分区不超过半数

```
partition:
old leader nodeB: 始终无法获得多数票,无法成功同步日志
new leader  nodeC: 成功同步日志

combine:
nodeB step down, log rollback;

```

![[Pasted image 20221127195106.png]]

case 2:
leader 所在分区 过半数。
其他分区无法选出新的leader,无法继续工作






## etcd 

### architecture;
![GMC94l](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220408/GMC94l.jpg)

1. basic:
   1. server(http,grpc): handle request;
   2. raft: common algorithm;
   3. storage: persist data;

2. what's wal
   ![LTMH1K](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211230/LTMH1K.jpg)
    write ahead log: 预写式日志；

3. comiteIndex vs applyIndex？
   1. 由两个独立组件维护:
      raft engine: update commitedIndex;
      store eneign: update  applyIndex, 
   2.  applyIndex <= commitedIndex

### basic  operation

1. get set/get:
   ```shell
   etcdctl put a b;
   etcdctl get a;
   etcdctl del a;
   etcd get --from-key 0; # range 
   ```

2. set expire;
    1. grant lease
    2. put key value --lease
    ```shell
    etcdctl lease grant 100 
    etcdctl put foo gogo --lease 12f77c78fd5f4b27
    etcdctl lease keep-alive 12f77c78fd5f4b27
    ```
3. watch:

   ```shell
   etcd watch foo;
   ```


