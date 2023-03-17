---
title: "Cpu"
date: "2021-04-01T17:01:29+08:00"
draft: false
---
[计算机科学速成课40集全](https://www.bilibili.com/video/BV1EW411u7thp=12)

[Computer - CPU(Central Processing Unit)](https://www.tutorialspoint.com/computer_fundamentals/computer_cpu.htm)

[Architecture of the central processing unit (CPU)](https://computersciencewiki.org/index.php/Architecture_of_the_central_processing_unit_(CPU))

[Different Classes of CPU Registers](https://www.geeksforgeeks.org/different-classes-of-cpu-registers/)

[计算机指令在CPU中的执行过程（图文版）](https://blog.csdn.net/jizhu4873/article/details/84393905)

[CPU 执行程序的秘密，藏在了这 15 张图里](https://www.cnblogs.com/xiaolincoding/p/13796525.html)

[MIPS_Instruction_Set](https://www.dsi.unive.it/~gasparetto/materials/MIPS_Instruction_Set.pdf)

> everything is about  caculation


## what's program;

program = algorithm(function) + data;


## von noenuman architecture

![](https://www.tutorialspoint.com/computer_fundamentals/images/computer_architecture.jpg)

1. control unit:
    包含:progarm counter register, instruction register;
    读取和解码指令，执行指令
    
    > 32bit cpu 能存储 最大内存地址 4g(42ebyte)
2. arithmetic/login unit:
   完成实际的计算
3. register(cache):
    store data
highlight:

> 程序指令也装进内存里,相比早期写死在控制单元里，具有很大灵活性；

## instruction

### 1. type

| type                                                                                     | work component                      |
| ---------------------------------------------------------------------------------------- | ----------------------------------- |
| caculate                                                                                 | alu                                 |
| jump;call/return                                                                         | alu; control unit(uncontional jump) |
| data transfrom(register and ram, register and register, ram and ram, registr and device) | register,ram                        |

### 2. how jump/call excute
非顺序指令流的执行；

![Br9sK4](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210705/Br9sK4.png)
![MM3f9D](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210705/MM3f9D.jpg)



#### 1. jump; jumpEqual;

`jump 1000`: jump to the address 1000;
`beq$1,$2,1000`: if register $1 and $2 is equal, go to address 1000



1. 干什什么?
     非顺序指令(更新progarm counter的值)；
1. 哪些命令用到jump: 条件语句；循环语句； 



### instruction structure

![lDvztt](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210705/lDvztt.jpg)

the element of instruction: operatecode , register , address/value

## the excuting of cpu

instruction cycle：
get instruction --->  decode ----> excute ----> store

1. control unit  根据 program counter, 从ram 读取指令；
2. control unit 解码指令；
3. control unit 控制 LRU和 register 执行指令
4. program counter 自增 指令长度，回到1重新执行

a = 1+2 指令执行过程
![QFMgNE](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210705/QFMgNE.jpg)

```shell
load 0x100 R0  # 加载数据 0x100 value from memory to register
load  0x104 R1 # 
add R0 R1 R2
set R2  0X108
```

 



## register


### 1. program counter 

hold the address of the  next instruction;

pc = pc + (the length of instruction)






## cpu load

1. load: the total task to excute: the number of toask being excuted
2. cpu utilization:
   idea time: 1- idea time - io wait time

case:
    1. a and b both 100% cpu, then weo shoud check load average to judge which is more busy
    2. high load average and low cpu utilization:  cpu have lost of time  in io waiting,




## the cpu cycle

![wLeEzP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210718/wLeEzP.jpg)

1. what is cyle
    cycle: a  time range to do sth  
    1. instruction cyle:  the cycle of a instruction
    2. clock cycle: the cpu's  minimum work time, a clock signal's time, a 2ghz cpu, the times of clock cycle is 2g ;
    3. cpu/machine cycle: a  set of clock cycle

2. why only the number of excuctions and memory space is considered when evaluating algorithm efficiency?

when cpu work, it consume the clock cycle,  it hope use clock cycle to do more things;
for progame, it should reduce the instructions's count,e.g., excutions's count

when ram work, it consume the memroy, it hope use less memroy to do more thing## the cpu cycle

![wLeEzP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210718/wLeEzP.jpg)

1. what is cyle
    cycle: a  time range to do sth  
    1. instruction cyle:  the cycle of a instruction
    2. clock cycle: the cpu's  minimum work time, a clock signal's time, a 2ghz cpu, the times of clock cycle is 2g ;
    3. cpu/machine cycle: a  set of clock cycle

2. why only the number of excuctions and memory space is considered when evaluating algorithm efficiency?

when cpu work, it consume the clock cycle,  it hope use clock cycle to do more things;
for progame, it should reduce the instructions's count,e.g., excutions's count

when ram work, it consume the memroy, it hope use less memroy to do more thing



## 时钟周期(clock cycle)
[为什么 CPU 需要时钟才能工作？](https://www.zhihu.com/question/21981280)
cpu 和 ram 都是有有一组组的逻辑电路

cpu工作过程就是逻辑门状态更改的过程；

一个cpu时钟就是cpu工作的最小单位，也就是逻辑门发生一次状态更改；






## instruction;

### pause

1. spin wait

   busy waiting: repeatedly check to see if a condition is true;
   while(a >0){};


2. PAUSE: a hint   to the processor that the code sequeue is a spin wait 
    1.   The processor uses this hint to avoid the memory order violation in most situations, which greatly improves processor performance
    2.  reduce the power consumed by a processor while executing a spin loop.

3. code
   ```
       // dospin定义，调用了procyield方法
   active_spin_cnt = 30

   func sync_runtime_doSpin() {
   	procyield(active_spin_cnt)
   }

   // procyield定义
   func procyield(cycles uint32)

   // 实现, 即将对ax寄存器放入30，减到0之后就算自旋完成一次返回
   TEXT runtime·procyield(SB),NOSPLIT,$0-0
   	MOVL	cycles+0(FP), AX
   again:
   	PAUSE
   	SUBL	$1, AX
   	JNZ	again
   	RET
   ```


