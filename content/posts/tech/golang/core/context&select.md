---
title: context && select
date: "2021-01-11T11:39:24+0800"
draft: false
categories: ["Go"]
---
[Go Concurrency Patterns: Context](https://blog.golang.org/context)

[深度解密Go语言之context](https://zhuanlan.zhihu.com/p/68792989)





context:  cancel a batch of g; pass value to  specify-scoped of g;
channel： share data between g
select： receive/send data from/to mutiple channel;
>  cancel or pass data;


what: go 提供的线程安全的共享信号和变量 


相比全局变量：
1.  block a group of goroutine 
2.  thread safe


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


1. web server, pass request info: userid
```
context.withValue("userID", 123 )

func haneleRequest(ctx){

}

```

2. cancel   a set of goroutine,  a long request g usually have a context to exit in early
```
// request 
func httpRequest(ctx,url):
	go dolong(chan,url)
	select{
		resp := <-chan:
				return resp	
		<-ctx.done
			return err
	}



```






## how it works

contxt value: 
使用不可变值确保线程安全 
```go
type struct{
	parent Contxt
	key, value any
} 
WithValue(ctx, key,value) Context:
	newCtx = struct{parent:ctx, key:key,value:value }
	return newCtx 
	 

func (c *valueCtx) Value(key interface{}) interface{} {

	if c.key == key {
		return c.val
	}
	return c.Context.Value(key)
}
```


cancel ctx: by close empty channel 
```bash
ctx:
	cancelChannel
	[]childCtx

Done:
	return ctx.cancelChanel

cancel:
	close(ctx.channel)
	for child range  childCtx:
		child.cancel

withCancel:
	return func(){
		cancel()
	}
	
```


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



## select 
use case:
1. cancel long job 
```
select{
	<-ctx.Done()
	<- 
}
```
1. 多个 g

```go
select {
	<-cancel.Done():
	return
	resut := <- request():
}



// wait mutiple g 
select {

	<-channe1:
	<- channel2

}

```




how:

1. 随机遍历所有channel, 选择一个可以 读写
2. 

1.  sleep current g:
2.  



2. how:
    
    1.  check channel is readable/writeable == YES: 
        select random one, read data;
    2. addCurrentG to all channel queue, gopak currentG;

    3. beReadyBy one Channel, pop from all channel;

