---
title: "如何将一段文本变成org-mode中的列表"
categories: [ "Vim" ]
tags: [ "emacs","org-mode" ]
draft: false
slug: "how-to-turn-a-piece-of-text-into-a-list-in-org-mode"
date: "2021-01-10 19:55:00"
---

假设有这么一段文本

```
thing one
thing two
thing three
etc 
```

<!--more-->


那么先全选在通过 `C-c -` 转换成
```
- thing one
- thing two
- thing three
- etc
``` 
也可以通过先全选,再通过 `C-u C-x r N RET DEL . SPC RET` 转换成
```
1. thing one
2. thing two
3. thing three
4. etc  
```