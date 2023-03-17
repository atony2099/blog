---
title: binary
date: "2021-10-19T14:50:27+0800"
draft: false
categories: ["base"]
---
[Addition using Bitwise Operations](https://iq.opengenus.org/addition-using-bitwise-operations/)

## float  binary represent

1.  integer vs float
    1.  integer: for n>0 { n =  n%2  }: Get remainder circularly 
        > 1: 00 00 00 00
    2.  float: for n>0{ n= GetFloatPart(n*2) }: get decimal circularly
        > 0.75 : 0.11
  
    > float binary represent maybe ultimate; 0.1= (0.00011...0011)


2. IEEE 754
   a standard for represent float value; 

    [sign 1bit][exponent 11bit][float  52bit]
    
    a = (-1)^sign *2^(exponent-1023)(float);

    2.1= 10.00011...0011 = (-1)^0 * 2^ 1024-1023)(00011..0011)


3. compare float
    1. math.Abs(1.1 - 1.2) <= 1e-9;


## represent of   positive and negative  integer value

1. origin 
   
    1:  0 0 00 00 01;

    -1: 1 0 00 00 00;


2. One’s Complement(反码)

    1:  00 00 00 01 = origin

   -1: 11 11 11 10 = origin Reverse except First bit

    1-1: 11 11 11 11 = -0

    > cons: have two types of 0's  represent: 00000000 and 11111111

3. two's complement(补码)

    1:  0 0 00 00 01 = origin

    -1: 1 1 11 11 11 = origin Reverse except First bit And add 1;   
    
    1-1:0 0 00 00 00 


## bit operator


| Operation       | Result   | Description                                 |
| :-------------- | :------- | :------------------------------------------ |
| `0011 & 0101`   | 0001     | Bitwise AND                                 |
| `0011 | 0101`   | 0111     | Bitwise OR                                  |
| `0011 ^ 0101`   | 0110     | Bitwise XOR  (the same is 0,different is 1) |
| `^0101`         | 1010     | Bitwise NOT (same as `1111 ^ 0101`)         |
| `0011 &^ 0101`  | 0010     | Bitclear (AND NOT)                          |
| `00110101<<2`   | 11010100 | Left shift                                  |
| `00110101<<100` | 00000000 | No upper limit on shift count               |
| `00110101>>2`   | 00001101 | Right shift                                 |



### bitwise addition

//todo?
```
def Bitwise_add(a,b):
    while b != 0:
        carry = a&b # Carry value is calculated 
        a = a^b # Sum value is calculated and stored in a
        b = carry<<1 # The carry value is shifted towards left by a bit

    return a # returns the final sum

```


