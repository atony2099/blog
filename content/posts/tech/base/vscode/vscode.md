---
title: vscode
date: "2021-06-01T18:08:23+0800"
draft: false
categories: ["vscode"]
---

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
