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
