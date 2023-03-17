# enum

[Ultimate Visual Guide to Go Enums and iota](https://blog.learngoprogramming.com/golang-const-type-enums-iota-bc4befd096d3)

[Representing enums in go](https://dev.to/shiraazm/representing-enums-in-go-21dp)

## what is enum?

Enumeration Type:

一组常量，同属于一种类型



## 相比常量
> 枚举是常量的升级版

### 1. 自动类型校验，不需要额外检查

```java
// before
public void describe(int s){
    //s变量不能超越边界，校验条件
    if(s >= 0 && s <4){
        switch(s){
            case Season.Summer:
                System.out.println("Summer is very hot!");
                break;
            case Season.Winter:
                System.out.println("Winter is very cold!");
                break;
        .....
        }
    }
}

public void describe(Season s){
    switch(s){
        case Season.Summer:
            System.out.println("Summer is very hot!");
            break;
        case Season.Winter:
            System.out.println("Winter is very cold!");
            break;
       ......
    }
}
```

### 2. 有内置方法，并可以自定义方法

```java
 public static void main(String[] args){ 
     for(Season s:Season.values()){ 
         System.out.println(s); 
     } 
 }

enum Season{
    Spring,Summer,Autumn,Winter;
    //最舒服的季节
    public static Season getComfortableSeason(){
        return Spring;
    }
}
```


### 1. js实现

```
const Color = {
   RED: 1,
   BLUE: 2,
   GREEN: 3,
   YELLOW: 4
};
Object.freeze(Color);
// Example usage:
let carColor = Color.BLUE;
// Trying to add new colors fails silently:
Color.WHITE = 5;
console.log(Color)

```















## itoa

1. The iota keyword represents successive integer constants 0, 1, 2,…
2. It resets to 0 whenever the word const appears in the source code,
3. and increments after each const specification.



## golang 模仿同一个enum



```golang
// 1. 定义类型
type State int 


// 2. 枚举值
const (
    Start  State  = itoa
    Doing  State 
    Finish State
)


// 3. 方法
func (s State)String()string{
    names := [...]string{
        "Start",
        "Doing",
        "Finish",
    }

    if s < Start || s > Finish {
        return "Unkonwn"
    }
    return names[s]

}

func (s State) isValid() bool {
  return  s > Start && s < Finish
}

```

由于 











