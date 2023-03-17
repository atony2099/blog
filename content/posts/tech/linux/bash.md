---
title: bash syntax
date: "2021-04-11T18:11:12+0800"
draft: false
categories: ["linux"]
---



## evaluating expression

### 1. shell expansion

1. brace expansion

    ```
    a{b,c,d}
    # ab ac ad

    a{1..2} #a1,a2
   1 a{1..5..2} #a1,a3
    ```

2. parameter expansion

   ```
    ${parameter};
    $parameter
   ```

3. output command

  ```
    a =  $(cat file )
    a = `cat file`

  ```

## other

### double quote vs single quote, curly bracket, parenthese

1. quote: <br/>
   '': keep word literal meaning
   "": expand $,\,`

    ```bash
    echo "$(echo hello)"  # hello
    echo '$(echo hello)' #   '$(echo hello)'
    ```
