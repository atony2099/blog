---
title: 人生和代码
date: "2021-10-24T15:57:46+0800"
draft: false
categories: ["thought"]
---
> 关于人生的随想，想到什么写什么

## 大脑是一个程序
大脑和代码运行机制是一样的,都是根据输入产生输出:function(x)=y;

只是我们大脑这个程序是在不断升级中,对同一输入可能会有不同输出；

in 1990  f(x) =y1;
in 2000  f(x) =y2;
y1 != y1'



## 幸福没有最优解；
幸福的算法是否有最优解呢? 在同样硬件配置(物质精神条件)是有最优解,但现实中每个人硬件配置千差万别，酷睿i9上的幸福算法不适合奔腾486电脑上；

```js
function HappinessCaculate(event) {
    let HappinessValue = 100; // 长期幸福感受
    let shortTermHappinessList = ["糖", "烟", "酒", "游戏", "性", "其他"] // 短期快乐但有害长期幸福
    let LongTermHappinessList = ["学习", "锻炼", "工作", "其他"] // 有益于长期幸福

    if (shortTermHappinessList.includes(event)) {
        HappinessValue -= 1
    }
    if (LongTermHappinessList.includes(event)) {
        HappinessValue += 1
    }


    if (LongtermHappiness <= 0) {
        console.log("Life is over")

    }
    if (LongtermHappiness > 100) {
        console.log("You have a good state")
    }
}
```



## 关于爱情
