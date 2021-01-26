---
title: "使用JavaScript更改具有!important声明的样式规则"
categories: [ "JS" ]
tags: [ "javascript","!important" ]
draft: false
slug: "using-javascript-to-change-the-style-rule-that-has-a-important-declaration"
date: "2011-11-02 19:55:00"
---

如果在CSS中使用!important属性声明规则，那么在JavaScript中需要使用cssText属性对样式进行修改。

如果元素仅有一条规则，可以直接将新规则（带!important声明）复制给cssText：

    element.style.cssText = 'display:inline !important';

如果元素具备多条规则，则需要将新规则加到cssText中：

    element.style.cssText += 'display:inline !important';


<!--more-->


*参考资料：*

[Overriding !important style using Javascript]()