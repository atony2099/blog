---
title: typescript
date: "2021-01-14T10:58:32+0800"
draft: false
categories: ["js"]
---

[声明文件](https://ts.xcatliu.com/basics/declaration-files.html#export-default)


## what is type script

add type system on top layer of js;



## for primitive Data



## for compound Data 

use interface to describe tyep  

```ts
interface User {
  name: string;
  id: number;
}
// option properties
interface SquareConfig {
  color?: string;
  width?: number;
}

```

### use interface as a  function  type

```js
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function (source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
};
```

### use interfae as class type

```js
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) {}
}
```


## union type

```ts
let a: true | "1";
a = true ;
a = "1"

```


## generic

in a nutshell,  generic enable types to be parameters when defining classes, methods, ionterfaces


1. generic interface type
```ts
interface Dog<T>{
    name: T
}

var d: Dog<string> = {name:"123"}
```

2. generic function type

interface EatFunc<T>{
(arg:T):T;
}

function Eattt<T>(arg:T):T {
    return arg
}
let me:EatFunc<number> = Eattt
```

3. generic class type

```ts
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};
```

## .d.ts

declare file;
用于调用非Typescript 库

  
```
delcare   

```

