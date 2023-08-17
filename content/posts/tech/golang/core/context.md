---
title: context && select
date: "2021-01-11T11:39:24+0800"
draft: false
categories: ["Go"]
---
[Go Concurrency Patterns: Context](https://blog.golang.org/context)

[深度解密Go语言之context](https://zhuanlan.zhihu.com/p/68792989)



what: go 提供的线程安全的共享信号和变量 

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
2. 使用自定义类型防止冲突

```go

type key1 string




func stroe(ctx context){
	var a key1 = "uid"
	context.WithValue(context.Background(),a,"123")
}
 
func handler(ctx){
	var a key1 = "uid"
	if id, ok := ctx.WithValue(a).(string);ok {
			queryDB(id)
	}
}
```


2. 取消一组 goroutine   

```go
ctx.WithTimeout()
go doWork(ctx)
go doWork(ctx)



func doWork(ctx){
	select {
		<-ctx.done:
			
		<- time.After(time.Second):

	}
}





```

1. 设置超时时间
```

func longrequest(){

	select{
		<-ctx.Done()
		<- longRunTaskChannel
	
		
	}
}

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
	
	// create a new valueCtx
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



## select 

what: wait a group of g, 
1. 批量等待channel
2. 同时监听多个channel 



use case:
1. 需要提前退出 
2. 多个 g

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

