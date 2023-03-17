---
title: template in Go
date: "2020-11-28T15:33:52+0800"
draft: false
categories: ["Go"]
---
## what is 

根据变量输出动态字符串

## syntax

1. data evaluate
`{{.Name}}`

2. control flow:
   range; if  else; 

```go
{{if .Xxxx}}
....
{{else}}

{{end}}

```

3. call function(methods)

```go
{{printf "%q" "output"}}
```


predifined function

```go
{{slice .List}}
{{index .List 1}}

```



4. trim the space from the preceding and following text;

```go
{{- xxx -}}
```