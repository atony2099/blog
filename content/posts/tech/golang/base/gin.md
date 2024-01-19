---
title: gin
date: 2024-01-18T18:05:07+08:00
lastmod: 2024-01-18T18:05:07+08:00
categories:
  - base
tags:
  - go
  - gin
---

what's  context;

how the gin implement

```
class GinContext {
    // Represents the HTTP request.
    Request HTTPRequest

    // Represents the HTTP response writer.
    Response HTTPResponseWriter

    // A map to store values specific to this context.
    Map[string]any Values

    // Status code to be sent in the response.
    int StatusCode

    // Handlers are the list of middleware + main handler that will be executed.
    List[HandlerFunc] Handlers

    // Index indicates the current position in the middleware chain.
    int Index

    // Context from the standard library, used for handling request cancellations.
    Context context.Context
}


```




request cancel:

what:  user cancel the   request;
when:
1. user actively cancel
2.  client reach the timeout 



how to detect it:
in go server , use  request.context;

```go
func handleRequest(w http.ResponseWriter, r *http.Request) {
	i := 0
	for {
		select {
		case <-r.Context().Done():
			fmt.Println("Client disconnected")
			w.WriteHeader(http.StatusNoContent)
			return
		default:
			fmt.Println("Working...")
			time.Sleep(1 * time.Second)
			i++
			if i == 5 {
				w.WriteHeader(http.StatusOK)
				w.Write([]byte("ok"))
				return
			}
		}
	}
}
```

how to response:
1. not return anything, beacuse the client won't recevie the message;
2. can return statsu 204 for logging 


## gin 

1. how gin work 
2. how gin middleware work
3. what the gin abort mean?


how gin  work?

```
class GinContext {
    // Represents the HTTP request.
    Request HTTPRequest

    // Represents the HTTP response writer.
    Response HTTPResponseWriter
	int Index

}

```


middleware: 
1. how it work ;
2. how abort work ？
```
handles = [handles1, handle2]

next(gin gin.Context){
	gin.Index++
	gin.handlers[index](gin) // pass control to next handle  

}



use(middleware){
	c.handels.append(handle )
}



```

the excute order:

```

func 1 
	 1 do sth;
	 next:
	      func 2:
		      2 do sth;
		      next // if next is nothing, just continue
		    2 do sth

	 1 do sth

```

abort , just stop remianing handles , but current handler is excute

```

abort:
	gin.index=-1


func 1:
	do sth 1;
	abort()
	next:
		  func2: // not be excuted

    do sth 2

```