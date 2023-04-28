---
title: "编译，链接，装载"
date: 2020-03-30T21:26:51+08:00
draft: false
categories: ["编译原理"]
---

[The Four Stages of Compiling a C Program](https://www.calleerlandsson.com/the-four-stages-of-compiling-a-c-program/)

[Introduction of Compiler Design](https://www.geeksforgeeks.org/introduction-of-compiler-design/)
[走进Golang之编译器原理](https://juejin.im/post/5dccb05ee51d4510ba708ff1#heading-0)

[目标文件里面到底有什么(1)？](https://blog.csdn.net/shenziheng1/article/details/52658431)
[Go: Overview of the Compiler](https://medium.com/a-journey-with-go/go-overview-of-the-compiler-4e5a153ca889)
[【Linux】编译,链接,装载简单梳理](https://www.jianshu.com/p/c248a29a47ed)
[Go 程序是怎样跑起来的](https://juejin.im/post/6844903879054737422#heading-4)
[我需要知道：编译、链接、装载、运行](https://blog.piasy.com/2017/10/24/I-Need-Know-About-Compile-Link-Load-Execute/index.html)
[程序的编译、链接、装载与运行](https://www.nosuchfield.com/2018/11/23/Program-compilation-linking-loading-and-running/)
[Compiler Architecture
](https://cs.lmu.edu/~ray/notes/compilerarchitecture/)
[1 What is a Compiler?](http://www.cs.umd.edu/class/spring2021/cmsc838E/Intro.html)

## 编译过程 （以c语言为例）
![](https://static.javatpoint.com/cpages/images/compilation-process-in-c2.png)
![](https://img-blog.csdnimg.cn/20190515225629812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RheWxvclBvdHRlcg==,size_16,color_FFFFFF,t_70)


hello.c
```c
/*
 * "Hello, World!": A classic.
 */

#include <stdio.h>

int
main(void)
{
	puts("Hello, World!");
	return 0;
}

```

1. 预编译: 
    1. 处理预编译指令；
    2. 导入 # include 引用的声明代码
    3. 移除注释
```c
[lines omitted for brevity]

extern int __vsnprintf_chk (char * restrict, size_t,
       int, size_t, const char * restrict, va_list);
# 493 "/usr/include/stdio.h" 2 3 4
# 2 "hello_world.c" 2

int
main(void) {
 puts("Hello, World!");
 return 0;
}
```

2. 编译: 转换成汇编
```c
    .section    __TEXT,__text,regular,pure_instructions
    .macosx_version_min 10, 10
    .globl  _main
    .align  4, 0x90
_main:                                  ## @main
    .cfi_startproc
## BB#0:
    pushq   %rbp
Ltmp0:
    .cfi_def_cfa_offset 16
Ltmp1:
    .cfi_offset %rbp, -16
    movq    %rsp, %rbp
Ltmp2:
    .cfi_def_cfa_register %rbp
    subq    $16, %rsp
    leaq    L_.str(%rip), %rdi
    movl    $0, -4(%rbp)
    callq   _puts
    xorl    %ecx, %ecx
    movl    %eax, -8(%rbp)          ## 4-byte Spill
    movl    %ecx, %eax
    addq    $16, %rsp
    popq    %rbp
    retq
    .cfi_endproc

    .section    __TEXT,__cstring,cstring_literals
L_.str:                                 ## @.str
    .asciz  "Hello, World!"

.subsections_via_symbols

```

3. 汇编:转换二进制格式文件(目标文件  Executable Linkable Format)

4. 链接:集成引用的依赖代码

   ![9rWXb4](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201019/9rWXb4.jpg)

   
   1. 静态链接: 在编译时集成引用的依赖
   2. 动态链接：在运行时集成
      优势:
      	1. 节省存储和磁盘空间
       	2. 热更新




### ELF 文件
excutable and linkable format 

是一种通用的格式


![MGtAKO](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201019/MGtAKO.jpg)

1. headerfile: 元信息
2. section(segment)
   1. text 代码
   2. data 数据(全局变量和局部静态变量)




## 装载(load)和执行(excute)

执行程序的过程

![I0PKtl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20201019/I0PKtl.jpg)

 

## linker

![XAu1pw](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220904/XAu1pw.jpg)

1. library
    1. binary 
    1. pre-compile code
    2. contain a set of function


2. dymanic vs static？
    1. static: locked into a program at runtime
    2. dynamic:exist as separate file 







## the compile  detail 

![s3bTS0](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20200516/s3bTS0.jpg)
![uOTKIn](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210920/uOTKIn.jpg)
![2ns1kA](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210920/2ns1kA.jpg)

### overview 
编译过程
1. 生成抽象语法树(ast); 并检查和简化；
2. 根据ast 生成中间代码从而进一步生成binary code;


### 词法分析（Lexical Analysis）

tokenList

生成 <token-name, value>

```go
package main
const s = "foo"
```

```
PACKAGE(package) 
IDENT(main) 
CONST(const) 
IDENT(s) 
ASSIGN(=) 
STRING("foo") 
EOF() 
```



###  语法分析
生成AST(abstract syntax tree)

![](https://user-gold-cdn.xitu.io/2019/11/14/16e67902feb85391?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 语义分析

类型检查

1. 检查语法树是否符合语法规则；例如值是否赋值给正确的类型。
2. 其他一些可能的优化工作





## ast tree

![ggNicd](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211116/ggNicd.jpg)

```c
while b ≠ 0:
    if a > b:
        a := a - b
    else:
        b := b - a
return a
```
1. what's 
    1. abstract syntax tree;
    2. describe code in a tree;
    3. used to generate assmebly code;;




node}->node
 |
node



```go
// All node types implement the Node interface.
type Node interface {
    Pos() token.Pos // position of first character belonging to the node
    End() token.Pos // position of first character immediately after the node
}

// All expression nodes implement the Expr interface.
type Expr interface {
    Node
    exprNode()
}

// All statement nodes implement the Stmt interface.
type Stmt interface {
    Node
    stmtNode()
}

// All declaration nodes implement the Decl interface.
type Decl interface {
    Node
    declNode()
}
```




