---
title: Assembly
date: "2021-09-11T12:30:22+0800"
draft: false
categories: ["linux"]
---

[Assembly Programming Tutorial](https://www.tutorialspoint.com/assembly_programming/index.htm)

[函数调用之堆栈原理（二）](https://zhuanlan.zhihu.com/p/54954221)

[C/C++内存对齐详解](https://zhuanlan.zhihu.com/p/30007037)

[Data Alignment](http://books.gigatux.nl/mirror/kerneldevelopment/0672327201/ch19lev1sec3.html)

[关于内存对齐，看我](https://juejin.cn/post/6870162226032934926#heading-2)

[What is data alignment? - WolfSound](https://thewolfsound.com/what-is-data-alignment/)


## memory alignment


what: 
将数据排列在特定内存位置，以加快cpu访问数据的速度
a way of arranging data in memory so that it can be accessed faster by the processo


why：
cpu 按照 word(32bit and 64bit)读取数据，如果不按照特定的位置排列好数据， 一个数据耗费更多次cpu时间去读取



example:

alignment
![n8GUMZFfNgHD](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230424/n8GUMZFfNgHD.jpg)

non-alignment
![Q7GoAgsPs88Z](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230424/Q7GoAgsPs88Z.jpg)




## how  to alignment


### primitive  type 
放置在特地位置

-   short: 2 bytes (aligned to even addresses)  
    short：2 字节（与偶数地址对齐）
-   int: 4 bytes (aligned to addresses divisible by 4)  
    int：4字节（对齐到可被4整除的地址）


###  non-primitive
add padding: 插入填充字节

```
//32位系统
#include<stdio.h>
struct
{
    int i;    
    char c1;  
    char c2;  
}x1;

struct{
    char c1;  
    int i;    
    char c2;  
}x2;

struct{
    char c1;  
    char c2; 
    int i;    
}x3;

int main()
{
    printf("%d\n",sizeof(x1));  // 输出8
    printf("%d\n",sizeof(x2));  // 输出12
    printf("%d\n",sizeof(x3));  // 输出8
    return 0;
}
```

![z2rJDq](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211115/z2rJDq.jpg)




## command 

### stack frame

1.  rbp: stack frame bottom;
2.  rsp: stack frame top, chaning as stack grow;


##  functon  call

```
add(int, int):
   0x080483b4 <+0>:	push   %ebp
   0x080483b5 <+1>:	mov    %esp,%ebp
   0x080483b7 <+3>:	sub    $0x4,%esp
   0x080483ba <+6>:	mov    0xc(%ebp),%eax
   0x080483bd <+9>:	mov    0x8(%ebp),%edx
   0x080483c0 <+12>:	add    %edx,%eax
   0x080483c2 <+14>:	mov    %eax,-0x4(%ebp)
   0x080483c5 <+17>:	mov    -0x4(%ebp),%eax
   0x080483c8 <+20>:	leave  
   0x080483c9 <+21>:	ret   
main:
   0x080483ca <+0>:	push   %ebp
   0x080483cb <+1>:	mov    %esp,%ebp
   0x080483cd <+3>:	sub    $0xc,%esp
   0x080483d0 <+6>:	movl   $0x2,0x4(%esp)
   0x080483d8 <+14>:	movl   $0x28,(%esp)
   0x080483df <+21>:	call   0x80483b4 <add>
   0x080483e4 <+26>:	mov    %eax,-0x4(%ebp)
   0x080483e7 <+29>:	leave  
   0x080483e8 <+30>:	ret    

```

## 2. stack

stackFrame:  a stack segment;
[return address, lastBasePointer,local variable,args]


1.  push Next address: 将该方法执行完之后需要继续执行的指令地址压入栈中；
2.  jump: 


 ```c
int add(int a, int b)
{
	int result = a + b;
	return result;
}

int main(int argc)
{
	int answer;
	answer = add(40, 2);
}



```c 
main;  
    push %ebp; #老的栈底存储起来
    move %esp %ebp;  #更新新的栈底值
    sub  $12 %esp; #栈空间增加 12byte；
    mov  $2 4(%esp); #2入栈
    mov  $40 %esp; #40入栈
    call add;
    mov   %eax,-0x4(%ebp)
    ret


add:
    push %ebp; #老栈顶存起来
    move %esp %ebp;  #更新新的栈底值
    sub   $0x4,%esp
    move 12(%ebp) %eax;
    move 8(%ebp) %edx;
    add %edx %eax; # 计算写入 %eax
    move %eax (-4)%ebp 
    move (-4)%ebp  %eax;
    leave 
    ret


leave; 
leave;
ret; #program counter
# 栈顶值更新为当前栈底；
# 栈底更新为老的；
# program counter 更新为新的指令；


move %eax  (-4)%ebp #将结果写入answer

leave;
leave;
ret;
```
