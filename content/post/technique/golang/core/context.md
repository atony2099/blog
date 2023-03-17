---
title: context && select
date: "2021-01-11T11:39:24+0800"
draft: false
categories: ["Go"]
---
[Go Concurrency Patterns: Context](https://blog.golang.org/context)
[深度解密Go语言之context](https://zhuanlan.zhihu.com/p/68792989)


## what is context?


```go
type Context interface {
    Done() <-chan struct{} // return a will be be closed channel;
    Err() error;
    Deadline() (deadline time.Time, ok bool)
    Value(key interface{}) interface{}
}
```
![Gg2khI](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211113/Gg2khI.jpg)


1. what?    


    pass value, cancel signal between  G;

    context: func cancelChann), func Value()

2. types?
    1.   value: valueCtx
    2.   signal:  cancelCtx->timerCtx;

3. features?
   1. thread safe; 
   2. cancel mutiple g at one time;


## use case;

### 1. how to use?
1.  pass as funtion paramters, pass in functin chain;


### 1. pass value between G:
1. 在协程之间传递值;
    ```
    ctx = newCtc(oldCtx,"requestID","123")
    doRequest:
        gid, requestID:= 456, ctx.value(requestID)


    ```

### 2. pass cancel signal betwen  G


case: 
   1. producer cancel  signal 
   2. for a loop

how:

    ```
    select:
        <- normal channle
        <- cancel context;
    ```




## how it works?

1. how:
    1.immutable value: 创建父子关系链；新的context不改变旧ctx的任何值
        `newContex{parentCtx,newValue, newCloseSignal}`




2. code
   ```go
    type valueCtx struct {
	Context
	key, val any
    }
    
    type cancelCtx struct {
	Context

	mu       sync.Mutex            // protects following fields
	done     atomic.Value          // of chan struct{}, created lazily, closed by first cancel call
	children map[canceler]struct{} // set to nil by the first cancel call
	err      error                 // set to non-nil by the first cancel call
    }
    ```




### 1. cancel signal
close channel

    ```go
    for child := range c.children {
        close(child.done)
	}
    ```
### 2. pass value
    ```go
    func WithValue(parent Context, key, val any) Context {
	if parent == nil {
		panic("cannot create context from nil parent")
	}
	if key == nil {
		panic("nil key")
	}
	if !reflectlite.TypeOf(key).Comparable() {
		panic("key is not comparable")
	}
	return &valueCtx{parent, key, val}
    }


    func (c *valueCtx) Value(key interface{}) interface{} {
	    if c.key == key {
		    return c.val
	    }
	    return c.Context.Value(key)
    }


    ```

## exit  loop;


1. in repeated job

    for {
        select {
          case  <- stopSignal // ctx.Done():
         default:
         // do other thing

        }
    }


2. in long  job;
    isCancle(){
        select{
       case ctx.Donec/<-chan: return true
       case default:return false
        }
    }
   

    step1: do sth
    check isCancle();
    step2: do sth



## select?
1. what?
   一个线程监听多个channel; go版本的多路复用；


2. how:
    
    1.  check channel is readable/writeable == YES: 
        select random one, read data;
    2. addCurrentG to all channel queue, gopak currentG;

    3. beReadyBy one Channel, pop from all channel;

3. use case:
    1. 监听cancel signal;
    2. 监听多个channel  
        select: 
            <-errorChannel;
            <-valueChannel;