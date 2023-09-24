---
title:  inteface
date: 2023-09-24T21:46:19+08:00
lastmod: 2023-09-24T21:46:19+08:00
categories: ["base"]
tags: [""]
---

## what

inreface 



## feature


1. in interface,  go auto take the address but not auto derefernce 

```go
type Name struct {
	firstName string
	lastName  string
}

func (n Name) foo() string {
	return n.firstName + " " + n.lastName
}

type Goo interface {
	foo() string
}




func main() {

	var a Goo = &Name{"foo", "bar"} // auto take the address 
	fmt.Println(a.foo())  

```

