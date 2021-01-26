---
title: "vscode 下使用 Fira Code字体"
categories: [ "JS" ]
tags: [ "fonts","vscode","fira code" ]
draft: false
slug: "use-fira-code-font-under-vscode"
date: "2019-12-22 15:25:00"
---

## 安装 `Fira Code`字体：

在[https://github.com/tonsky/FiraCode](https://github.com/tonsky/FiraCode)下载字体,如图：

![mbioq_fira_code_vscode.png][1]

然后解压并并安装.

## 配置字体 


<!--more-->


```json
// 控制字体系列。
  "editor.fontFamily": "'Fira Code',Consolas, 'Courier New', monospace",
  // 启用字体连字
  "editor.fontLigatures": true,
  // 以像素为单位控制字号。
  "editor.fontSize": 18,
  // 控制字体粗细。
  "editor.fontWeight": "normal", //这个设置字体粗细，可选normal,bold,"100"~"900"等，选择合适的就行
  "editor.cursorStyle": "line-thin",
  "editor.lineHeight": 24,
  "editor.lineNumbers": "on",
  "editor.minimap.enabled": false,
  "editor.renderIndentGuides": false,
  "editor.rulers": [
    120
  ],
  "editor.insertSpace": 2,
  "editor.detectIndentation": false,
  "editor.tabSize": 2,
  "editor.quickSuggestions": {
    "other": true,
    "comments": true,
    "strings": true
  },
```

  [1]: https://imgs.gnux.cn/usr/uploads/2019/12/3212376075.png