---
title: eslint
date: "2021-03-24T23:52:12+0800"
draft: false
categories: ["js"]
---

[Create custom ESLint rules in 2 minutes](https://www.webiny.com/blog/create-custom-eslint-rules-in-2-minutes-e3d41cb6a9a0)

## rules


eslint define a set of rules 
rules will throw a error or warning when encounter possible problems,stylistic issues

```json
{
  "rules": {
    "camelcase": 2,
    "quotes": [2, "single"],

}
```

rules work only you explicitly declare it



## extend 

load a set of rules without declaring them



## plugin 
a extend of offical rules.
To make it work, you should declare or extend it




## perttier and eslint

1. eslint-config-prettier: turn off all eslint rules that are conflict with prettier 
2. eslint-plugin-prettier: run prettier from within eslint


```json
{
  "extends": ["prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error",
    "arrow-body-style": "off",
    "prefer-arrow-callback": "off"
  }
}
```