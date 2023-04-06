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

how:
1. stop all open and idle  resouce  
2. wait running  work  to finish 


### http server:

```go
package main

import (
	"context"
	"log"
	"net/http"
	"os"
	"os/signal"
	"time"
)

func main() {

	srv := &http.Server{Addr: ":8090"}

	go func() {
		if err := srv.ListenAndServe(); err != http.ErrServerClosed {
			log.Fatalf("ListenAndServe(): %v", err)
		}
	}()
	// Setting up signal capturing
	stop := make(chan os.Signal, 1)
	signal.Notify(stop, os.Interrupt, os.Kill)
	<-stop

	ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
	defer cancel()
	if err := srv.Shutdown(ctx); err != nil {
		log.Fatal("Server Shutdown Failded:", err)
	}
	log.Println("Server exiting gracefully")
}

```

shutdown:
1.  closing all open  listeners
2. close  all idle connections,
3. **wait  indefinitely for connections to return to idle and then shut down**,  so you have to set canceled timeout  



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
