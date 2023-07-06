

---
title: "map"
date: 2020-05-14T11:39:03+08:00
draft: false
tags: ["go","hash"]
lastmod: 2023-04-18T16:47:26+0800
---

[Map实现原理分析](https://studygolang.com/articles/27421)

[深度解密Go语言之 map](https://juejin.im/post/6844903848587296781#heading-8)

[由浅到深，入门Go语言Map实现原理](https://segmentfault.com/a/1190000039101378)

[golang map底层实现](http://yangxikun.github.io/golang/2019/10/07/golang-map.html)

[哈希表](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-hashmap/#33-%E5%93%88%E5%B8%8C%E8%A1%A8)

[深入理解 Go map：赋值和扩容迁移](https://segmentfault.com/a/1190000018632347)

[深度解密Go语言之 map](https://zhuanlan.zhihu.com/p/66676224)

[map 的实现原理 | Go 程序员面试笔试宝典](https://golang.design/go-questions/map/principal/)

[遍历过程 | Go 程序员面试笔试宝典](https://golang.design/go-questions/map/range/)

create：
1. 字面量 创建:使用常量创建
2. 函数创建: make 

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

v, ok =m["1"]
```

## how

key pointer:
1.  hash func 
2.  hash collision: 使用改进版的链表法，节点嵌套数组，减少内存创建和回收的cost

```go
index = hashCode % bucklength


if  buckets[index].full == no:
	buckets[index].append(key,value)
else:
	bucket = buckets[index]
	for:
		bucket = bucket.next
		if bucket == nil:
			break; 

		if bucket.full == no 
			buckets[index].append(key,value)
			break;
	
	

	
```



### base structure


![3Tiheke075SD](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230418/3Tiheke075SD.jpg)


simple
```
hmap->[]bucket

bucket:
	[]key
	[]value
	*overflowBucket

```


```go
type hmap struct {
	count     int
	flags     uint8
	B         uint8  // 创建 2 ^ B bucket
	noverflow uint16 // overflow 的 bucket 近似数
	hash0     uint32
	buckets    unsafe.Pointer // []bmap
	oldbuckets unsafe.Poiner

	nevacuate  uintptr
	extra *mapextra 
}

// bucket
type bmap struct {
    tophash [8]uint8
    keys     [8]keytype
    values   [8]valuetype
    pad      uintptr
    overflow uintptr // next bmap 
}
```



### hash fun 


![enOaHr](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/enOaHr.jpg)

64bit:
1. last  2^b bit  code  to index bucket
2. first 8 bit  code to index bmap.array

```
if cpu support aes instruction: hash with aes
use memhash 
```



###  overflow

how:  
1. linker node contain  a array,
2. 提前分配好 extra []bmap

![rerUTy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/rerUTy.jpg)
![NrnglL](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210901/NrnglL.jpg)




### access a key

1. 遍历数组和overflow 
2. compare tophash and key

```go
for {
	    // 遍历 bucket 的 8 个位置
		for i := uintptr(0); i < bucketCnt; i++ {
		    // tophash 不匹配，继续
			if b.tophash[i] != top {
				continue
			}
			// tophash 匹配，定位到 key 的位置
			k := add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
			// key 是指针
			if t.indirectkey {
			    // 解引用
				k = *((*unsafe.Pointer)(k))
			}
			// 如果 key 相等
			if alg.equal(key, k) {
			    // 定位到 value 的位置
				v := add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.valuesize))
				// value 解引用
				if t.indirectvalue {
					v = *((*unsafe.Pointer)(v))
				}
				return v
			}
		}
		
		// bucket 找完（还没找到），继续到 overflow bucket 里找
		b = b.overflow(t)
		// overflow bucket 也找完了，说明没有目标 key
		// 返回零值
		if b == nil {
			return unsafe.Pointer(&zeroVal[0])
		}
	}
```




## rehash

what:  为了防止查询效率退化(too many overflow)，通过增加bucket ，删除多余的overflow


监控指标， when:
1. load fator >  6.5, 核心指标
2. overflow bucket too  many: 补充指标,特殊情况

how:
1.   allocate new bucket
2.   渐进式迁移，在更新或者删除处罚变迁动作
	
	
```go
func hashGrow(t *maptype, h *hmap) {
	// B+1 相当于是原来 2 倍的空间
	bigger := uint8(1)

	// 对应条件 2
	if !overLoadFactor(int64(h.count), h.B) {
		// 进行等量的内存扩容，所以 B 不变
		bigger = 0
		h.flags |= sameSizeGrow
	}
	// 将老 buckets 挂到 buckets 上
	oldbuckets := h.buckets
	// 申请新的 buckets 空间
	newbuckets, nextOverflow := makeBucketArray(t, h.B+bigger)

	flags := h.flags &^ (iterator | oldIterator)
	if h.flags&iterator != 0 {
		flags |= oldIterator
	}
	// 提交 grow 的动作
	h.B += bigger
	h.flags = flags
	h.oldbuckets = oldbuckets
	h.buckets = newbuckets
	// 搬迁进度为 0
	h.nevacuate = 0
	// overflow buckets 数为 0
	h.noverflow = 0

	// ……
}
```

```go
func mapdelete(t *maptype, h *hmap, key unsafe.Pointer) {
	.....

  bucket := hash & bucketMask(h.B)
  if h.growing() {
    growWork(t, h, bucket)
  }
}

func growWork(t *maptype, h *hmap, bucket uintptr) {
  // 搬迁正在使用的旧 bucket
  evacuate(t, h, bucket&h.oldbucketmask())
  // 再搬迁一个 bucket，以加快搬迁进程
  if h.growing() {
    evacuate(t, h, h.nevacuate)
  }
}

func (h *hmap) growing() bool {
  return h.oldbuckets != nil
}

```



### factor > 6.5

what:  buckets 使用情况.   used_buckets/total_buckets


why 6.5:  空间和时间权衡
1. 空间: 65% bucket have used
2. 时间: 20% overflow

```bash
// Picking loadFactor: too large and we have lots of overflow
// buckets, too small and we waste a lot of space. I wrote
// a simple program to check some stats for different loads:
// (64-bit, 8 byte keys and values)
//  loadFactor    %overflow  bytes/entry     hitprobe    missprobe
//        4.00         2.13        20.77         3.00         4.00
//        4.50         4.05        17.30         3.25         4.50
//        5.00         6.85        14.77         3.50         5.00
//        5.50        10.55        12.94         3.75         5.50
//        6.00        15.27        11.67         4.00         6.00
//        6.50        20.90        10.79         4.25         6.50
//        7.00        27.14        10.15         4.50         7.00
//        7.50        34.03         9.73         4.75         7.50
//        8.00        41.10         9.40         5.00         8.00
//
// %overflow   = percentage of buckets which have an overflow bucket
// bytes/entry = overhead bytes used per key/value pair
// hitprobe    = # of entries to check when looking up a present key
// missprobe   = # of entries to check when looking up an absent key
```

how: 
1. bucket * 2 
2. 重新计算 index;
3. lazy move: 当发生（删除，修改，插入） ,  一次移动一个bucket
  ![4eqSJQ](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/4eqSJQ.jpg)

  ![IVWhNW](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/IVWhNW.jpg)


###  too many overflow 

what: 对 load factor 的补充, 
```go
func tooManyOverflowBuckets(noverflow uint16, B uint8) bool {
	if B < 16 {  // overflow 和bucket一样多
		return noverflow >= uint16(1)<<B
	}
	return noverflow >= 1<<15
}
```

漏网之鱼: 通过删除从而无法达到 6.5







###  how 

```
deleteMap:
	moveOneBucket
setMap:
	moveOneBucket

moveIndex = 0
moveOneBucket:
	
	for ;  oldBucket!= nil;oldBucket = oldBuckets[index].overflow;:
		
	moveIndex++;

```



how: 
![RAPA3x](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/RAPA3x.jpg)

![uNRSW3](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211111/uNRSW3.jpg)




   

## 遍历过程以及无序

###  traverse
generate random start bucket index  and offset 

```go
r := uintptr(fastrand())
if h.B > 31-bucketCntBits {
	r += uintptr(fastrand()) << 31
}

// 从哪个 bucket 开始遍历
it.startBucket = r & (uintptr(1)<<h.B - 1)
// 从 bucket 的哪个 key 开始遍历
it.offset = uint8(r >> h.B & (bucketCnt - 1))
```


2. 开始遍历,  如果 in 迁移 state, 检测对应的老bucket 是否迁移过
	1. 未迁移， 则遍历老bucket 



![Qmod6tEuJ2Ud](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230423/Qmod6tEuJ2Ud.png)


![ZjjTHOWNOCxe](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230423/ZjjTHOWNOCxe.jpg)


### unorder 

why:
1. map 是一个不稳定结构， 常常需要扩容， 导致，key的顺序经常发生改变
2.  go在遍历开始生成随机的遍历起始位置，为避免给新手程序员误解




