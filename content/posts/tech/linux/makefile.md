---
title: makefile
date: "2021-01-23T14:00:57+0800"
draft: false
categories: ["makefile"]
---
[makefiletutorial](https://makefiletutorial.com/)
[what-how-makefile](https://opensource.com/article/18/8/what-how-makefile)
[A Good Makefile for Go](https://kodfabrik.com/journal/a-good-makefile-for-go/)

## what makefile do

    main purpose: comiple c,c++ file into a binary file;
    other purpose: combine  a serious of task into  a specific task;

## syntax

```
Do: requisite1, requisite2...
    task1;
    task2;
    task3;
```

Do: the target file OR the action Name
requisite:  the dependent files;
task

```
hello: hello.go
    build hello.go -o hello
hello.go:
    echo "println("hello")" > hello.go
```

### PHONEY

makefile task won't  excute every time, it will check target file and source file;

if it think it's not  neccesary to excute, it will throw out "update to date"

PHONEY: tell make that the target isn't a file , it's just a task name, so the task will excute every time
