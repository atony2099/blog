---
title: "map"
date: 2020-05-14T11:39:03+08:00
draft: false
tags: ["go","hash"]
---

[Map实现原理分析](https://studygolang.com/articles/27421)

[深度解密Go语言之 map](https://juejin.im/post/6844903848587296781#heading-8)

[由浅到深，入门Go语言Map实现原理](https://segmentfault.com/a/1190000039101378)

[golang map底层实现](http://yangxikun.github.io/golang/2019/10/07/golang-map.html)

[哈希表](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-hashmap/#33-%E5%93%88%E5%B8%8C%E8%A1%A8)

[现在面试都这么直接的嘛？(golang map)](https://chowdera.com/2021/04/20210420190818405w.html)

[深入理解 Go map：赋值和扩容迁移](https://segmentfault.com/a/1190000018632347)

[深度解密Go语言之 map](https://zhuanlan.zhihu.com/p/66676224)



create：
1. 字面量 创建
2. 函数创建 

```go
	//--1. init 
    // 1.literal 
    var a = map[int]int{1:1, 2:2};

    // 2. make 
    var b = make(map[int]int);


```
basic operation:
```go
delete(m,"1")

for k,v :=  range m

```


## how

![enOaHr](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/enOaHr.jpg)
![rerUTy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/rerUTy.jpg)
![NrnglL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/NrnglL.jpg)


1. get   index value
2.  get bucket index, store by tophash value ;




### base structure


hash truct:
mpas->buckets-> bmap linker  

```go
type hmap struct {
	count     int
	flags     uint8
	B         uint8
	noverflow uint16
	hash0     uint32
	buckets    unsafe.Pointer
	oldbuckets unsafe.Poiner

	nevacuate  uintptr
	extra *mapextra 
}

// bucket
type bmap struct {
    topbits  [8]uint8
    keys     [8]keytype
    values   [8]valuetype
    pad      uintptr
    overflow uintptr // next bmap 
}
```




###  hash value 

64bit
1. last 2^B bit for bucket index
2. first 8bit, tophash for  bmap index 



### store by tophash
 search first empty slot in node, or  search in next node; 
 store by tophash;




## rehash

what:
casue:  map length update->  bucket 




### linker too  long/big
cause:
1.   element increase:  increase bucket， 增量扩容
2.   elemnt increase but delte later in map: delte linker , 等量扩容

why:
search  linker time    increase 

solve:
1.  increase bucket
2. decrase linker 

### bucket too long 

cause:
element decrease ;

why:
take up more space 


solve:
decrease bucket len, 缩容

		




2. goal: reassign entries to  reduce the length of linked-list
增量扩容: bucketCount = currentCount* 2;
等量缩容: bucketCount = currentCount && reduce overflowsCount;

###  grow, 扩容

what:
grow bucket

when:
loadFacor =  elementCount/bucketCount > 6.5;

why:
linker grow big/long, reduce time of search linker 



how grow:
1. grow buckets: double  oldBucket
2. reput key by new hash code

incremental grow:
    copy bucket only if bucket is update/add/delete;


4. how 
  ![4eqSJQ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/4eqSJQ.jpg)

  ![IVWhNW](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/IVWhNW.jpg)


###  shrink 等量扩容 

what:
delte linker node


when: 
tooMuch overflow ; overflow >= bucket.length
```go
// overflow buckets 太多
func tooManyOverflowBuckets(noverflow uint16, B uint8) bool {
if B < 16 {
return noverflow >= uint16(1)<<B
}
return noverflow >= 1<<15
}

```

2. why:
     too long linker....

3. when happen
    add much key,then delete;

4. how?
   ![RAPA3x](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/RAPA3x.jpg)

   ![uNRSW3](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/uNRSW3.jpg)

## unoder

why:

1. map进行扩容导致 bucket顺序发生改变
2.  go add random code  when  traversal: startBuckIndex = random 
```go 
  func mapiterinit(t *maptype, h *hmap, it *hiter) { 
            r := uintptr(fastrand())
         	if h.B > 31-bucketCntBits {
         		r += uintptr(fastrand()) << 31
         	}
	    it.startBucket = r & bucketMask(h.B)
}
```






