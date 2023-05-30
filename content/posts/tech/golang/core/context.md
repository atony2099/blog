---
title: context && select
date: "2021-01-11T11:39:24+0800"
draft: false
categories: ["Go"]
---
[Go Concurrency Patterns: Context](https://blog.golang.org/context)

[深度解密Go语言之context](https://zhuanlan.zhihu.com/p/68792989)

在一定范围(1 或者多个 goroutine): 
1.  传递值, pass value ;
2. 传递信号, pass signal;

相比全局变量：
1.  可以传递信号
2.  线程安全的 


```go
type Context interface {
    Done() <-chan struct{} // return a will be be closed channel;
    Err() error;
    Deadline() (deadline time.Time, ok bool)
    Value(key interface{}) interface{}
}

type canceler interface {
	cancel(removeFromParent bool, err, cause error)
	Done() <-chan struct{}
}
```
![Gg2khI](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211113/Gg2khI.jpg)


pass value

```go
ctx  := context.WithValue("123","123")

value := ctx.Value("123")

```


pass signal

```go
ctx, cancel := context.WithCanel(ctx)
ctx, cacel : =context.WithTimeout(ctx)
```



    pass value, cancel signal between  G;

    context: func cancelChann), func Value()

2. types?
    1.   value: valueCtx
    2.   signal:  cancelCtx->timerCtx;

3. features?
   1. thread safe; 
   2. cancel mutiple g at one time;


## use case
1. 携带request-scoped  value 

```go

context.WithValue(context.Background(),"uid","123")

func handler(ctx){
	if id, ok := ctx.WithValue("uid").(string);ok {
			queryDB(id)
	}
}
```


1. 取消一组 goroutine  

```go
go doWork(ctx)
go doWork(ctx)


func doWork(ctx){
	
}





```
1.  设置 耗时操作的超时时间








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




## how it works
### value:
1. 使用组合继承父类属性；
2. 递归查询 
3.  immutable context 确保线程安全
```go
	type valueCtx struct {
	Context
	key, val any
	}
    

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


### cancel  signal 
使用 close(chan)
```go
type cancelCtx struct {
	Context
	mu       sync.Mutex            // protects following fields
	done     atomic.Value          // of chan struct{}, created lazily, closed by first cancel call
	children map[canceler]struct{} // set to nil by the first cancel call
	err      error                 // set to non-nil by the first cancel call
	cause    error                 // set to non-nil by the first cancel call
}

func (c *cancelCtx) cancel(removeFromParent bool, err, cause error){
	if err == nil {
		panic("context: internal error: missing cancel error")
	}
	if cause == nil {
		cause = err
	}
	c.mu.Lock()
	if c.err != nil {
		c.mu.Unlock()
		return // already canceled
	}
	c.err = err
	c.cause = cause
	d, _ := c.done.Load().(chan struct{})
	if d == nil {
		c.done.Store(closedchan)
	} else {
		close(d)
	}
	for child := range c.children {
		// NOTE: acquiring the child's lock while holding parent's lock.
		child.cancel(false, err, cause)
	}
	c.children = nil
	c.mu.Unlock()

	if removeFromParent {
		removeChild(c.Context, c)
	}
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