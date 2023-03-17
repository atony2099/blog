---
title: etcd and servicve discovery
date: "2021-01-13T11:44:04+0800"
draft: false
categories: ["micro"]
---

[go操作etcd](https://www.kancloud.cn/golang_programe/golang/1172700#_253)

## what
distributed key-value store:   
1. strongly consistently
2. use raft 

vs redis:
same: key value store

differ:
1. 使用场景：
	1. redis: 存储业务数据,快速
	2. etcd: 存储关键数据, 可靠

## arhitecture:
![HpWOI3](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/HpWOI3.jpg)

componetnt:
1.  grpc  server
2. raft algorithm 
3. store:
	1. wal 
	2. boltdbd: key  value storeage





##  basic operation
```c
#set 
etcdctl put a b

# get  
etcdclt get a 
etcdctl get  "" --prefix // get all keys 

// read old version  
etcd get a  --rev=5 // the max revision  is 5;





#  expired time 
etcdctl lease grant 100
etcdctl lease keep-alive  leaseid
etcdctl put a b --lease  leaseid

# del 
etcdctl del a 



```




## service discovery

how:
1. register:  put  and   keepalive with ticker  
2. unregister: delete  key
4.  client:  getService:
	1. getKeyWithPrefix
	2. watch with prefix

```
etcdctl lease grant  20 
etcdctl  put /services/shoping/192  192  --lease xxxxx
etcdctl lease keep-alive  xxxxxx

etcdctl get --prefix /services/shopping  --> add local serviceList
etcdctl watch --prefix /services/shopping：
	if delete, remove from local list
	if put, addd to local luis

```


```go
func  Register?(){

	func updateIP (){
		   r.etcd3Client.Put(ctx, key, value, etcd3.WithLease(resp.ID));

	}
	
 
	c = time.NewTicker

	for {
		case <-canclel
			return
			
		case <-c:
			
						
	}
		
}

func  UNRegister?(){
	etcdClient.Revoke(lease)
	//or delete 
	r.client.Delete(....)
}

func  GetServuces(){
	
}

func  Watch(){

	c := watchWithPreifix

	select {
		case PUT:
		case Del：
			
	
	}
}