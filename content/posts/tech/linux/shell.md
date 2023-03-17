---
title: shell
date: "2021-04-11T18:11:12+0800"
draft: false
categories: ["linux"]
---

[shell](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html);

## what's shell

 1. a program which interactive with linux ; list:
    1. sh(Bourne shell)
    2. bash
    3. zsh
    4. dash
    5. ...

 1. /bin/sh
   a linker to a actual shell language

    ```shell
    !# /bin/sh   // a program will run the script
    ```

    ```
    echo $0 // the name of the shell language
    ```

## shell basic

1. IFS: input field seperator, default value: space, tab newline

## define variables

1. export vs not export

```
export a=1;  # can access in child process
a=1; # only access in current process
```

## shell type

1. login vs nonlogin
    1. login: first login into pc，run once,need password
        1. ssh root@...
        2. bash --login
    2. non-login: no need password
         1. everytime open a new termial in linux system
         2. bash

2. interactive vs noninteractive
    1. interactive: run from user's input
    2. noninteractive: run from a prcoess;

## source file

### what

1. what?
   global config file for shell script;
    1. alias
    2. global variables.;

2. profile vs ..rc;
    1. profile:run once, global variables,
    2. run every time
        1. alias: alias ls='ls -la'
        2. function:  `pyn init`;

### profile vs bashrc

### 1.  bash

> in b stage, will read b1, b2,b3  by order, only read the first one

```
+----------------+-----------+-----------+------+
|                |Interactive|Interactive|Script|
|                |login      |non-login  |      |
+----------------+-----------+-----------+------+
|/etc/profile    |   A       |           |      |
+----------------+-----------+----------vi -+------+
|/etc/bashrc     |           |    A      |      |
+----------------+-----------+-----------+------+
|~/.bashrc       |           |    B      |      |
+----------------+-----------+-----------+------+
|~/.bash_profile |   B1      |           |      |
+----------------+-----------+-----------+------+
|~/.bash_login   |   B2      |           |      |
+----------------+-----------+-----------+------+
|~/.profile      |   B3      |           |      |
+----------------+-----------+-----------+------+
|BASH_ENV        |           |           |  A   |
+----------------+-----------+-----------+------+
|                |           |           |      |
+----------------+-----------+-----------+------+
|                |           |           |      |
+----------------+-----------+-----------+------+
|~/.bash_logout  |    C      |           |      |
+----------------+-----------+-----------+------
```

![z2UpgE](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210428/z2UpgE.jpg)

1. why sperate two kind of kind
    history reason
2. profile vs rc
    profile: login; load one time;
        1. global enviroment: `export  PATH:`
        2.
    bashrc: load every time; alias and functions
        1. alias rm='rm -i'

### zsh

```
+----------------+-----------+-----------+------+
|                |Interactive|Interactive|Script|
|                |login      |non-login  |      |
+----------------+-----------+-----------+------+
|/etc/zshenv     |    A      |    A      |  A   |
+----------------+-----------+-----------+------+
|~/.zshenv       |    B      |    B      |  B   |
+----------------+-----------+-----------+------+
|/etc/zprofile   |    C      |           |      |
+----------------+-----------+-----------+------+
|~/.zprofile     |    D      |           |      |
+----------------+-----------+-----------+------+
|/etc/zshrc      |    E      |    C      |      |
+----------------+-----------+-----------+------+
|~/.zshrc        |    F      |    D      |      |
+----------------+-----/------+-----------+------+
|/etc/zlogin     |    G      |           |      |
+----------------+-----------+-----------+------+
|~/.zlogin       |    H      |           |      |
+----------------+-----------+-----------+------+
|                |           |           |      |
+----------------+-----------+-----------+------+
|                |           |           |      |
+----------------+-----------+-----------+------+
|~/.zlogout      |    I      |           |      |
+----------------+-----------+-----------+------+
|/etc/zlogout    |    J      |           |      |
+----------------+-----------+-----------+------+
```

## some variables

1. PPID: parent id
2. $$ currrent id
3.

## syntax

### 1. evaluating expressions

### 1. expansion

### 1. variable

1. have no dataType explictly;

```bash
# integer or string
a=100
a=tang 
# array
a=(1 2 3)

readonly a=100 # readonly 
```

2. declare:  weak form of typing a variables

```bash
declare -i a; # integer
declare -a a; # array;
decare  -r a; #  readonly
```

3. unset: remove variable or function

### 2.function

1. get return value
   1. $?: last return or exit code

2. define a function

```shell
function name() {
    echo $1 $2
    return 123
}
name tang jim
echo $?
```

using return or exit to stop the excution

### 3. control flow(if else)

#### 1. if

```shell
if [ 1=1 ]; then
    echo true
else
    echo false
fi
```

|              Operator | Description                                                           |
| --------------------: | :-------------------------------------------------------------------- |
|          ! EXPRESSION | The EXPRESSION is false.                                              |
|             -n STRING | The length of STRING is greater than zero.                            |
|             -z STRING | The lengh of STRING is zero (ie it is empty).                         |
|     STRING1 = STRING2 | STRING1 is equal to STRING2                                           |
|    STRING1 != STRING2 | STRING1 is not equal to STRING2                                       |
| INTEGER1 -eq INTEGER2 | INTEGER1 is numerically equal to INTEGER2                             |
| INTEGER1 -gt INTEGER2 | INTEGER1 is numerically greater than INTEGER2                         |
| INTEGER1 -lt INTEGER2 | INTEGER1 is numerically less than INTEGER2                            |
|               -d FILE | FILE exists and is a directory.                                       |
|               -e FILE | FILE exists.                                                          |
|               -r FILE | FILE exists and the read permission is granted.                       |
|               -s FILE | FILE exists and it's size is greater than zero (ie. it is not empty). |
|               -w FILE | FILE exists and the write permission is granted.                      |
|               -x FILE | FILE exists and the execute permission is granted.                    |

#### 2. for...in

```shell
a=(1 2 3)
for v in "${a[@]}"; do
    echo "$v"
done
```

### create a custom command

1.

## lint

1. without  double quote, the value will be splited by IFS, the glob will be expanded, and the result will join by space

    ```shell
   a="a    b*"
   echo $a #a b.sh
    ```

## math arithmetic

* expr
* let or declare

```shell
    declare -i a; a+=100;
    let  a=0 a+=100;
 ```

* (());

    ```shell
    a=$((100+2))
    ```
