
---
title: parser
date: 2022-01-26
description:
tags:["go","parser"]
---



## what

![wdF2ac](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/wdF2ac.png)


how:
1. 词汇分析: token list
3.  语法分析: ast
4.  generate code




## ast  tree

what:   token之间的关系， 用树形式表示出来


![NlG037](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/NlG037.jpg)


parse tree vs abstract tree:  ast 是 parse tree 进一步提炼


## go  parse work

how:
1. goToken: getTokenList
2.  parser: getAst 








##   rules

the proces:
1. rules and datasources
2. parse 


parse: bool  表达式
```
 a  < 9 &&  b > 0
```
1. logic  ops:  ||,  &&
2. compare:  >=, <=, !=


list:
1. https://github.com/Knetic/govaluate
2. 



example:
a > 1 && (b <10 || a < 199)


binaryExp
- x: binaryExp 
	- x:ident,  a
	- y: literal . 1
	- op， >
- y: parentExp
	- x:binExp
		- x:binaryExp
			- x: ident, b
			- y:literal, 10
			- op, <
		- y:binaryExp
			- x: ident
			- y: literal 
			- op
		- op:  ||

```


     0  *ast.BinaryExpr {
     1  .  X: *ast.BinaryExpr {
     2  .  .  X: *ast.Ident {
     3  .  .  .  NamePos: -
     4  .  .  .  Name: "a"
     5  .  .  }
     6  .  .  OpPos: -
     7  .  .  Op: >
     8  .  .  Y: *ast.BasicLit {
     9  .  .  .  ValuePos: -
    10  .  .  .  Kind: INT
    11  .  .  .  Value: "1"
    12  .  .  }
    13  .  }
    14  .  OpPos: -
    15  .  Op: &&
    16  .  Y: *ast.ParenExpr {
    17  .  .  Lparen: -
    18  .  .  X: *ast.BinaryExpr {
    19  .  .  .  X: *ast.BinaryExpr {
    20  .  .  .  .  X: *ast.Ident {
    21  .  .  .  .  .  NamePos: -
    22  .  .  .  .  .  Name: "b"
    23  .  .  .  .  }
    24  .  .  .  .  OpPos: -
    25  .  .  .  .  Op: <
    26  .  .  .  .  Y: *ast.BasicLit {
    27  .  .  .  .  .  ValuePos: -
    28  .  .  .  .  .  Kind: INT
    29  .  .  .  .  .  Value: "10"
    30  .  .  .  .  }
    31  .  .  .  }
    32  .  .  .  OpPos: -
    33  .  .  .  Op: ||
    34  .  .  .  Y: *ast.BinaryExpr {
    35  .  .  .  .  X: *ast.Ident {
    36  .  .  .  .  .  NamePos: -
    37  .  .  .  .  .  Name: "a"
    38  .  .  .  .  }
    39  .  .  .  .  OpPos: -
    40  .  .  .  .  Op: <
    41  .  .  .  .  Y: *ast.BasicLit {
    42  .  .  .  .  .  ValuePos: -
    43  .  .  .  .  .  Kind: INT
    44  .  .  .  .  .  Value: "199"
    45  .  .  .  .  }
    46  .  .  .  }
    47  .  .  }
    48  .  .  Rparen: -
    49  .  }
    50  }

```




