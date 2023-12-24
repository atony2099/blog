---
title: "Test"
date: 2020-05-14T11:39:03+08:00
draft: false
---

[Test Driven Development: what it is, and what it is not.](https://www.freecodecamp.org/news/test-driven-development-what-it-is-and-what-it-is-not-41fa6bca02a2/)

## basic concept 

### unit test; integration test
![](https://m.media-amazon.com/images/G/01/DeveloperBlogs/AlexaBlogs/default/pyramid._CB471590882_.png)

1.  unit:
    small piece of code
2. integration test
   a gropu of unit 




## TTD
test driven development
1. write test
2. implement the code
3. Refactor 


test first development:

先写单元测试，再实现具体逻辑，再重构
以单元测试作为驱动；


## test sql 

1. sql mock

```golang



```

2. real test


## testMain

```
func TestMain(m *testing.M) {
	log.Println("Do stuff BEFORE the tests!")
	exitVal := m.Run()
	log.Println("Do stuff AFTER the tests!")

	os.Exit(exitVal)
}
```
test 执行开始或者之后做一些必要动作
1. 之前
   > //连接数据库

2. 之后
   > 关闭数据库




##  Test In GO;

```go
// run a  test
func  testFunc(t *testing.T){
   if reuslt  != expencted{
       t.Errorf("unexpected", expected)
   }
}


// run a set of  test
func testFunc (t *testing.T){

   T.Run(name, func(t *testing.T){
        if reuslt  != expencted{
       t.Errorf("unexpected", expected)
   }
   })
} 

```

## Mock in Go

interface:
   GetSth(a int) int


func (mockObj) GetSth() int{}





## command
go test  [source] [flags]
benchmark:


1. flages
   1. -count: run each test benchmark n times;
   2. 

2. test
   1. source
      1. local: filename
         `go test`, `go test a.go`
      2. package mode(will cache): . , ./.. packagename

3. benchmark
   ```
   BenchmarkFib10          483523              2458 ns/op             504 B/op          6 allocs/op
   # 2458: cost 2458ns
   # cost:504
   # cost: 6 allocation
   ```
   1. -bench=regex;
   2. -benchmem: memory data;




## test command 

1. types
   1. TestXxxx 
   2. BenchmarkXxxx
   3. ExampleXxxx: simple  test;



2.  Benchmark
    run methods N times,




## test in program


what's test in program:
通过执行一定的动作并与预期做比较从而得出结论

test types:
1.  unit test 
	1. 对象： 某个func 或者模块；
	3. mock:  需要mock:  当有内部模块依赖或者 外部依赖（数据库，网络）, 使用mock
	4. 白盒: 需要知道内部代码

1.   integration  test：
	1. 对象:   几个func 或者模块
	2. 使用真实的依赖
	3.  白盒：需要内部代码
2.  system test:  
	1. 整个 系统 
	2. 黑盒
		1. 可以 不需要代码， 直接使用使用 系统，  通过api调用 ，浏览器登方式  

单元测试和集成测试界限很模糊，调用了db 也可以叫单元测试


what's unit test:
通过主动调目标函数 并观察结果来进行的test
```
result  = callFunct()
if result != expected:
	print(error )
```



test typess

1. unit test:最小单位

unit test vs 其他: 单元测试，
1. 通常只测试一小块代码
2. 使用 mock 减少依赖


test in go:
1. 放在 _test文件中



test 形式:

1. single test 
```

var input, expect

result := runFun

if result != xx {
  t.Error("unexpect")
}
```

1. table test：  a series of tes 

1. run test parallel:  t.run





## test in go

1. mock io 