---
title:  beancount
date: 2023-05-19T00:02:49+08:00
lastmod: 2023-05-19T00:02:49+08:00
categories: ["non-tech"]
tags: [""]
---

[Beancount复式记账（一）：为什么 - https://byvoid.com/zhs/blog/beancount-bookkeeping-1/](https://byvoid.com/zhs/blog/beancount-bookkeeping-1/)

[使用 Beancount 记账篇零：Beancount 入门使用 | Verne in GitHub - https://einverne.github.io/post/2021/02/beancount-introduction.html](https://einverne.github.io/post/2021/02/beancount-introduction.html)


equity = (Assets+ income)- (liability+expense)





## record

record:
```fallback
[yyyy-MM-dd] [*|!] "payee" "备注"
  posting 1
  posting 2
```

basic:
```fallback
2019-01-01 * "日本航空" "纽约-东京"
  Expenses:Transport:Airline 1000 USD
  Liabilities:CreditCard:US:Discover -1000 USD
```

省略:
```fallback
2019-01-01 * "日本航空" "纽约-东京"
  Expenses:Transport:Airline 1000 USD
  Liabilities:CreditCard:US:Discover
```

多个 
```fallback
2019-01-01 * "Walmart" "在超市买两件衣服和晚餐"
  Expenses:Clothing 20 USD
  Expenses:Clothing 10 USD
  Expenses:Food:Dinner 10 USD
  Liabilities:CreditCard:US:Discover -40 USD
```

汇率转换 
```fallback
2019-01-01 * "日本航空" "纽约-东京"
  Expenses:Transport:Airline 1000 USD @ 110 JPY
  Liabilities:CreditCard:JP:Rakuten -110000 JPY

2019-01-01 * "日本航空" "购买里程"
  Assets:Points:Airline:JAL 10000 P_JAL @ 2.5 JPY
  Liabilities:CreditCard:US:Discover -220.0 USD @@ 25000 JPY
```




## 开设账户，初始化 

```
1990-01-01 open Assets:Card:1234 CNY, USD ;尾号1234的银行卡，支持CNY和USD
1990-01-01 open Liabilities:CreditCard:5678 CNY, USD ;双币信用卡
1990-01-01 open Income:Salary CNY ;工资收入
1990-01-01 open Expenses:Tax CNY ;交税
1990-01-01 open Expenses:Traffic:Taxi CNY ;打车消费，只支持CNY


;2、账户初始化 2019-08-27 * "" "银行卡，初始余额10000元" Assets:Card:1234 10000.00 CNY Equity:OpenBalance -10000.00 CNY
```


```
yyyy-MM-dd open 账户类型:命名:命名区别 货币[,货币2


```