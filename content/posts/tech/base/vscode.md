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

how it work
```js
if  file.save == true:
	formatTools(file, formatConfigure)
```

default  format  configure for  js
```json
"javascript.format.indentStyle": "space", "javascript.format.indentSize": 2, "javascript.format.insertSpaceAfterConstructor": false,
.....
```


vscode don't provide default format for all languate, 
you must
1. install extention 
2. custom the configure if need 


 js format:  use prettier, tabsize6
```json
"javascript.format.defaultFormatter": "esbenp.prettier-vscode",
"[javascript]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.tabSize": 6
}
```


go foramt: use goimports, tabsize6, linewidth 120
```json
"go.formatTool": "goimports",
"go.formatFlags": ["-tabs=false", "-tabwidth=6", "-max-width=120"]

```


python foramt: use black, python line length 120
```
"python.formatting.provider": "black",
"python.formatting.blackArgs": ["--line-length", "120"]

```


## Visuals
```json 
  "workbench.colorTheme": "Vitesse Dark",
  "workbench.preferredDarkColorTheme": "Vitesse Dark",
  "workbench.preferredLightColorTheme": "Vitesse Light",
  "workbench.productIconTheme": "icons-carbon",
  "workbench.iconTheme": "file-icons",
  "workbench.fontAliasing": "antialiased",
  "editor.fontFamily": "Input Mono, Fira Code, monospace",
  "editor.fontLigatures": "'ss01', 'ss02', 'ss03', 'ss06', 'zero'",
```




## nesting file

related  file nesting in the VS Code Explorer.
```json
"explorer.fileNesting.enabled": true, "explorer.fileNesting.expand": false, "explorer.fileNesting.patterns": {
	"go.mod": "go.sum",
}
```
\>go.mod
	\> go.sum