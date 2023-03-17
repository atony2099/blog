
---
title: compile
date: "2022-03-09T22:43:44+0800"
draft: false
categories: ["Go"]
---

[ASTs - What are they and how to use them](https://www.twilio.com/blog/abstract-syntax-trees)

## compile  step;

![cNGFyX](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220310/cNGFyX.jpg)

lexical analysis -> syntax analysis -> code generation

## ast tree


![hLHsx3](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220311/hLHsx3.jpg)
```c
if (x>y)
    max =x;
else 
    max = y;;
```

1. abstract tree: 
   1. convert code into tree
   2. abstract: not save all information, 

2. somethings discarded:s
    1. parenthesis;
    2. semicolons;
    3. else

3. node  types:
    1. statement;
    2. expression;
    3. declaration;


   ```c
   type Node interface {
       Pos() token.Pos // position of first character belonging to the node
       End() token.Pos // position of first character immediately after the node
   }
   ```



### binary expression tree


1. types
   1. algebraic expression
      ![2aFvl8](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220315/2aFvl8.jpg)
   2. boolean expression
      ![WqXdAV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220315/WqXdAV.jpg)


2. rules 
   1. binary boolean expression

    ```
    *ast.BinaryExpr {
     1  .  X: *ast.BinaryExpr {
     2  .  .  X: *ast.Ident {
     3  .  .  .  NamePos: -
     4  .  .  .  Name: "a"
     5  .  .  .  Obj: *ast.Object {
     6  .  .  .  .  Kind: bad
     7  .  .  .  .  Name: ""
     8  .  .  .  }
     9  .  .  }
    10  .  .  OpPos: -
    11  .  .  Op: >
    12  .  .  Y: *ast.BasicLit {
    13  .  .  .  ValuePos: -
    14  .  .  .  Kind: INT
    15  .  .  .  Value: "1"
    16  .  .  }
    17  .  }
    18  .  OpPos: -
    19  .  Op: &&
    20  .  Y: *ast.ParenExpr {
    21  .  .  Lparen: -
    22  .  .  X: *ast.BinaryExpr {
    23  .  .  .  X: *ast.BinaryExpr {
    24  .  .  .  .  X: *ast.Ident {
    25  .  .  .  .  .  NamePos: -
    26  .  .  .  .  .  Name: "b"
    27  .  .  .  .  .  Obj: *(obj @ 5)
    28  .  .  .  .  }
    29  .  .  .  .  OpPos: -
    30  .  .  .  .  Op: <
    31  .  .  .  .  Y: *ast.BasicLit {
    32  .  .  .  .  .  ValuePos: -
    33  .  .  .  .  .  Kind: INT
    34  .  .  .  .  .  Value: "1"
    35  .  .  .  .  }
    36  .  .  .  }
    37  .  .  .  OpPos: -
    38  .  .  .  Op: &&
    39  .  .  .  Y: *ast.BinaryExpr {
    40  .  .  .  .  X: *ast.Ident {
    41  .  .  .  .  .  NamePos: -
    42  .  .  .  .  .  Name: "a"
    43  .  .  .  .  .  Obj: *(obj @ 5)
    44  .  .  .  .  }
    45  .  .  .  .  OpPos: -
    46  .  .  .  .  Op: >
    47  .  .  .  .  Y: *ast.BasicLit {
    48  .  .  .  .  .  ValuePos: -
    49  .  .  .  .  .  Kind: INT
    50  .  .  .  .  .  Value: "3"
    51  .  .  .  .  }
    52  .  .  .  }
    53  .  .  }
    54  .  .  Rparen: -
    55  .  }
    56  }

    ```


3. code:
    ```
    postOrder(tree) bool:   
        if tree.isLeaf, return bool
           left=postOrder(tree.leftsubtree)
           right=postOrder(tree.rightsubtree)

           if tree.token is v:  
                return left v right;
           if tree.token is ^:    
                return left ^ riight;
        
    ```


        
    