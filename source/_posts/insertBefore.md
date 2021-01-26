---
title: "insertBefore"
categories: [ "JS" ]
tags: [ "javascript","insertbefore" ]
draft: false
slug: "insertbefore"
date: "2011-11-01 06:19:00"
---

`insertBefore()`方法可在已有的子节点前插入一个新的子节点。此方法可返回新的子节点。

    insertBefore(newchild, refchild)

在IE中如果没有refchild，那么insertBefore()方法第二个参数不要指定。
而在Firefox/Google Chrome等需要设定为null。

考虑往列表ul中插入列表项li：


<!--more-->


    var firstItem = ul.childNodes[0];
    if (!firstItem) { // 判断ul是否存在子元素
        document.all ? oUl.insertBefore(li) : ul.insertBefore(li, null);
    }else{
        ul.insertBefore(li, firstItem);
    }

*参考资料：*

[insertBefore Method - MSDN.aspx]()