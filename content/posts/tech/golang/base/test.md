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

