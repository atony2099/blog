---
title: vscode
date: "2021-06-01T18:08:23+0800"
categories: ["vscode"]
lastmod: 2023-04-04T13:57:08+0800
---


##   format 


action:
```json
editor.formatOnSave: true
```

how:
```js
if  file.save == true:
	formatTools(file, formatconfigure)
```


default  format  configure for  js
```json
"javascript.format.indentStyle": "space", "javascript.format.indentSize": 2, "javascript.format.insertSpaceAfterConstructor": false,
.....
```


change the format  tool and  congiure

```json

```





what: vscode use  default 

configure:
```json

```


default 





## setting.json

### 1. codeActionOnSave

allow which plugin to excute when saved

```json
 "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
        "source.fixAll.markdownlint": true
    }
```

### 2. set tab to space

```shell
// The number of spaces a tab is equal to. This setting is overridden
// based on the file contents when `editor.detectIndentation` is true.
"editor.tabSize": 4,

// Insert spaces when pressing Tab. This setting is overriden
// based on the file contents when `editor.detectIndentation` is true.
"editor.insertSpaces": true,

// When opening a file, `editor.tabSize` and `editor.insertSpaces`
// will be detected based on the file contents. Set to false to keep
// the values you've explicitly set, above.
"editor.detectIndentation": false

```

## set theme

```
⌘K ⌘T
```



