
---
title: regex
date: "2021-01-15T10:37:24+0800"
draft: false
categories: ["regex"]
---


match. a  type:
1. \\d: number
2. \\w: [a-z]  [0-9_]
3. \s: any whitespace
4.  .: match any charcter., except for \\n


match times:
1. ?:  [0,1]
2. +:[1, +unlimited]
3.  * : [0, +unlimited]



## 2. syntax



## base




| single char                               | quantifiers(数量)             | position(位置)             |
| ----------------------------------------- | ----------------------------- | -------------------------- |
| \d 匹配数字                               | * 0个或者更多                 | ^一行的开头                |
| \w  匹配word(数字、字母)                  | + 1个或更多，至少1个          | $一行的结尾                |
| \W 匹配**非**word(数字、字母)             | ? 0个或1个,一个Optional       | \b 单词"结界"(word bounds) |
| \s 匹配white space(包括空格、tab等)       | {min,max}出现次数在一个范围内 |                            |
| \S 匹配**非**white space(包括空格、tab等) | {n}匹配出现n次的              |                            |
| . 匹配任何，任何的字符                    |                               |                            |






### 1. character class(character set)

```
The lynk is quite a link don't you think? l nk l(nk
```

正则表达式： `l[yi (]nk`

结果:

```
lynk  link  l nk   l(nk
```



## match mode
[Specifying Modes Inside The Regular Expression(https://www.regular-expressions.info/modifiers.html)

Flag	Meaning
i	case-insensitive
m	let ^ and $ match begin/end line in addition to begin/end text (multi-line mode)
s	let . match \n (single-line mode)

(?i): case insensitive 
(?g): global

Normally, matching modes are specified outside the regular expression.

> /abc/i

如果语言不支持，可以这么定义
> (?i)abc



## greedy mode vs lazy mode


greedy mode: + or .

lazy mode: put ? after + or .


```
let regexp = /".+?"/g;

let str = 'a "witch" and her "broom" is one';

alert( str.match(regexp) ); // witch, broom
```
