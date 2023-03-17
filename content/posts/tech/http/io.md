---
title: io model
date: "2021-08-07T02:46:50+0800"
draft: false
tags: ["reactor","io"]
---

[unp](https://notes.shichao.io/unp/)

[https://www.geeksforgeeks.org/udp-server-client-implementation-c/](https://www.geeksforgeeks.org/udp-server-client-implementation-c/)

[Networking with C](https://medium.com/@chongye225/networking-with-c-cf15426cc270) 

[【操作系统】I/O 多路复用，select / poll / epoll 详解](https://imageslr.com/2020/02/27/select-poll-epoll.html#select)

[彻底理解 IO 多路复用实现机制](https://juejin.cn/post/6882984260672847879)

[Understanding Reactor Pattern Thread-Based and Event-Driven](https://github.com/pirDOL/kaka/blob/master/Miscellaneous/Understanding-Reactor-Pattern-Thread-Based-and-Event-Driven.md)

[Redis 多线程网络模型全面揭秘](https://segmentfault.com/a/1190000039223696)

[彻底搞懂Reactor模型和Proactor模型](https://cloud.tencent.com/developer/article/1488120)

[五分钟快速理解 Reactor 模型](https://jishuin.proginn.com/p/763bfbd58a63)

[Redis与Reactor模式](https://cloud.tencent.com/developer/article/1420724)

[高性能网络框架：Reactor 和 Proactor](https://bbs.huaweicloud.com/blogs/266248?utm_source=infoq&utm_medium=bbs-ex&utm_campaign=other&utm_content=content)

[Redis 多线程网络模型全面揭秘](https://segmentfault.com/a/1190000039223696)

[Go netpoller 原生网络模型之源码全面揭秘](https://strikefreedom.top/go-netpoll-io-multiplexing-reactor)

[IO多路复用到底是不是异步的？ - 闪客sun的回答 - 知乎](https://www.zhihu.com/question/59975081/answer/1932776593)

[The reactor pattern](https://subscription.packtpub.com/book/web-development/9781783287314/1/ch01lvl1sec09/the-reactor-pattern)

[Node js Event loop : Behind the scenes (Reactor pattern)](https://hashnode.com/post/node-js-event-loop-behind-the-scenes-reactor-pattern-cksds9t6r0eilu5s19gn49oct)

[Redis 多线程网络模型全面揭秘](https://strikefreedom.top/archives/multiple-threaded-network-model-in-redis)

## overview


readData
1. copy from internet to kernel: cost time
2. copy from kernal  to  userspace

core problem:
如何检测内核是否有数据了



## i/o 类型:
![nGk1wN](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/nGk1wN.jpg)


#### 1. blocking(thread-based)
 ![2HNDgD](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/2HNDgD.jpg)

 ```
list = [socket1,socket2];

for _,sk := list {
  go read(sk);
}

 ```



1. udp;
```c
var fd =new Socket();
bind(fd,address,port);
data =  receivefrom(fd);
```

2. tcp;


```c
listFD = new socket();
bind(listenFD)
listen(listenFD) 
while(1){
   // get ready conne
   acceptFd =  accept(bind);
   read(accetpFd, To: appBuffer);
   close(acceptFd)
}
```

### 2.non-blocking
   ![WmucXH](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/WmucXH.jpg)

### 3. multiplex
  ![yIdMTA](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/yIdMTA.jpg)

### 4. signal driven
   ![YKLTFN](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/YKLTFN.jpg)
### 5. async  
![bH6K0p](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210809/bH6K0p.jpg)


## multiplex


### 1. what

1. event notification(driven );
2. 使用一个线程监听多个socket是否就绪(ready)

```
blockWait(socketsList);

for _,s := socketsList{
  if s.state==ready{
    ....
  }
}

```




### 2. how 

#### 1.compare
1. select, poll: 通过轮询方式获取FD;
   1. select: bitmap，1024限制
   2. poll: 链表，无上限
   
2.  epoll:回调方式获取可读写FD

#### 1. select

```c
// 数据结构 (bitmap)
typedef struct {
    unsigned long fds_bits[__FDSET_LONGS];
} fd_set;

fds fd_set

while(1){

	select(rsets)

   for i=0; i<maxFd; i++{
      if (FD_ISSET(i, &rset)){
         	recvfrom(i,Buffer);
      }
   } 
```



#### 2. poll

```c
// 数据结构
struct pollfd {
    int fd;                         // 需要监视的文件描述符
    short events;                   // 需要内核监视的事件
    short revents;                  // 实际发生的事件
};

// API
int poll(struct pollfd fds[], nfds_t nfds, int timeout);

```

#### 3. epoll

```c
   int main(int argc, char* argv[])
{
    // 内核中创建ep对象
    epfd=epoll_create(256);


    // 需要监听的socket放到ep中
    epoll_ctl(epfd,EPOLL_CTL_ADD,listenfd,&ev);
 
    while(1) {
      // 阻塞获取
      nfds = epoll_wait(epfd,events,20,0);
      for(i=0;i<nfds;++i) {
          if(events[i].data.fd==listenfd) {
              // 这里处理accept事件
              connfd = accept(listenfd);
              // 接收新连接写到内核对象中
              epoll_ctl(epfd,EPOLL_CTL_ADD,connfd,&ev);
          } else if (events[i].events&EPOLLIN) {
              // 这里处理read事件
              read(sockfd, BUF, MAXLINE);
              //读完后准备写
              epoll_ctl(epfd,EPOLL_CTL_MOD,sockfd,&ev);
          } 
      }
    }
    return 0;
}

```



compare to select:
   1. 返回明确的fd, 不需要遍历整个bitmap;


## tcp server

the processs:




1. select fds: block
   1. newConnection:  newFd=accept(listenFd); set(newfd,fds);
   2. new ReadFd: read(ReadFd)


```c
#include <stdio.h>

#include <errno.h>

#include <stdlib.h>

#include <unistd.h>

#include <sys/types.h>

#include <sys/socket.h>

#include <netinet/in.h>

#include <netdb.h>

#define PORT 5555
#define MAXMSG 512

int read_from_client(int filedes) {
  char buffer[MAXMSG];
  int nbytes;

  nbytes = read(filedes, buffer, MAXMSG);
  if (nbytes < 0) {
    /* Read error. */
    perror("read");
    exit(EXIT_FAILURE);
  } else if (nbytes == 0)
    /* End-of-file. */
    return -1;
  else {
    /* Data read. */
    fprintf(stderr, "Server: got message: `%s'\n", buffer);
    return 0;
  }
}

int main(void) {
  extern int make_socket(uint16_t port);
  int sock;
  fd_set active_fd_set, read_fd_set;
  int i;
  struct sockaddr_in clientname;
  size_t size;

  /* Create the socket and set it up to accept connections. */
  sock = make_socket(PORT);
  if (listen(sock, 1) < 0) {
    perror("listen");
    exit(EXIT_FAILURE);
  }

  /* Initialize the set of active sockets. */
  FD_ZERO( & active_fd_set);
  FD_SET(sock, & active_fd_set);

  while (1) {
    /* Block until input arrives on one or more active sockets. */
    read_fd_set = active_fd_set;
    if (select(FD_SETSIZE, & read_fd_set, NULL, NULL, NULL) < 0) {
      perror("select");
      exit(EXIT_FAILURE);
    }

    /* Service all the sockets with input pending. */
    for (i = 0; i < FD_SETSIZE; ++i)
      if (FD_ISSET(i, & read_fd_set)) {
        if (i == sock) {
          /* Connection request on original socket. */
          int new;
          size = sizeof(clientname);
          new = accept(sock,
            (struct sockaddr * ) & clientname, &
            size);
          if (new < 0) {
            perror("accept");
            exit(EXIT_FAILURE);
          }
          fprintf(stderr,
            "Server: connect from host %s, port %hd.\n",
            inet_ntoa(clientname.sin_addr),
            ntohs(clientname.sin_port));
          FD_SET(new, & active_fd_set);
        } else {
          /* Data arriving on an already-connected socket. */
          if (read_from_client(i) < 0) {
            close(i);
            FD_CLR(i, & active_fd_set);
          }
        }
      }
  }
}
```




## network model
1. thread-based: thread-per-connection:

   ```c
   while true:
      var connect = accept(); 
      processConnectionInThread(connect)
   ```

   cons: create too many thread;

2. event-driven: 
   1. reactor 


## Reactor  patter

what:
design pattern, event-driven architecture



simple  code:
select -> handle;
```c

resources(event): to be handled data;


Reactor:  wait and  dispatch  resources
	Demultiplexer: select  resouces
	dispatcher: dispatch resources

handles: handle resources
	read, decode,compute,encode，write
	
```
event types:
1.  connect request 
2.  read  request


###  single thread
![JjbJiM](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/JjbJiM.jpg)

```
while true:
   ableHandle = select(handles);
   for handler in ableHandles:
         handler();   
handler:
   read;
   decode,computer,encode;
   write;
```
###  multi-thread
![2CDxug](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211030/2CDxug.jpg)

change: 数据处理交给子线程, 主线程处理handle 处理读和写 

```
while true:
   ableHandles = select(handles);
   for handle in ableHandles:
      data = read(fd); 
      theadPool.hanle(data);
      writeBack(data)

threadPool(data){
   decode;
   compute;
   encode;;
}
```


### master-worker   reactor

![hogMQF](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/hogMQF.jpg)


master Reactor： wait  accpetor socket , dispatch readSocket ;
sub Reactor: wait  ReadSock

```c
main thread:

	select(sockets)

	for socket range sockets
		if socket  is connectionRequest: 
			sockets.append(socket)

		if socket is readRequest:
			call subThread(socket)
						


SubThread1: readSocket
	 select(fds)

	 for fd  range fds
		 data = read(fd);
		 decode(data);
		 handle(data);
		 writedata to fd;


SubThread2:
	....

```


### master-worker-redis 


![o3yQmI](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/o3yQmI.jpg)




```

subReactor:
	 select(readrequests);
	 read
	 decode
			 .....
		

```

[[assemble]]

## Go and multiplex;

### 1.过程



### 2. go 是 reactor模型吗？



## Redis模型 and nginx




### 2. redis 为什么要多线程；
redis 是 单线程版本的reactor模型; 
单个线程承担阻塞和处理事件的任务； 并不能充分发挥多线程的优势；



分担一部分任务:read,decode, write;


```
while true:
   ableHandles = select(handles);
   for handler in ableHandles:
      data = read(fd)
      newThread:
         decode(data)

      excute(data);
      writeBack(data);
```


