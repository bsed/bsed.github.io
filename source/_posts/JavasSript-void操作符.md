---
title: "JavasSript void操作符"
categories: [ "JS" ]
tags: [ "javascript","void" ]
draft: false
slug: "void-javassript-operator"
date: "2010-10-19 20:05:00"
---

JavaScript中的void操作符语法如：

    void expression

void执行`expression`并返回 `undefined` 。

void常见的两个用途是：

 - 获取`undefined`变量
 - 作为href的伪协议


<!--more-->


在JavaScript中`undefined`是并非一个保留字，所以存在着被更改的可能，这也意味着直接通过判断给定的变量是否undefined并非可靠。所以此时可以通过void来**判断变量是否是undefined**：

    // Undescore中的isUndefined函数
    _.isUndefined = function (obj) {
        return obj === void 0;
    };

第二种用途中，作为html中href的JavaScript伪协议值。这个值将阻止页面进行跳转。

    <a href="javascript:void(0);">空链接</a>