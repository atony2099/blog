---
title: not null
date: "2021-01-23T19:22:08+0800"
draft: false
categories: ["mysql"]
---

## what is null?

indicate that the value is missing or unkonwn;

## compare with null

```sql
select NULL IS NULL # true
select NULL IS NOT  null # true
select NULL <=> NULL # true
```

but it will result in a null value when using  =, != <=, >=

## the problem of using null

### 1. select problem

1. null rows is eliminated from count function, and
    negative selection(such as !=, Not like, not in)

2. index problem
    index  need one extra byte to record nullable field
