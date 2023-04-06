---
title: stop go 
date: "2021-05-05T18:44:41+0800"
draft: false
categories: ["Go"]
---

## linux  signal

an important event happen;
send by kernel or other process(shell command);

## the list of signal

1 up: hang up
2 int (ctrl + c)
3 quit (ctrl + \)
9 kill: can't be caught and ignreo  
15 termination

## graceful stop go



### http server:





### 1. the conventional way to stop a program in linux

1. press keyboard
   crtl +c(interrupt); crtl+\(quit)
2. send a signal
   kill -15 pid; kil -9 pid

### 2. how to gracefully stop a program

catch all possible  signal, do some clean work before exiting

```
    ch := make(chan os.Signal, 1)
 signal.Notify(ch, syscall.SIGINT, syscall.SIGQUIT, syscall.SIGTERM)
 t := <-ch
 log.Println("clean work")
 os.Exit(0)
```
