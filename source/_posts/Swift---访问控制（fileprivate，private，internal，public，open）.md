---
title: "Swift - 访问控制（fileprivate，private，internal，public，open）"
categories: [ "IOS" ]
tags: [ "swift" ]
draft: false
slug: "swift-access-control-fileprivate-private-internal-public-open"
date: "2015-10-31 09:45:00"
---

在Swift语言中，访问修饰符有五种，分别为`fileprivate`，`private`，`internal`，`public`和`open`。
其中 `fileprivate`和`open`是Swift 3新添加的。由于过去 Swift对于访问权限的控制，不是基于类的，而是基于文件的。这样会有问题，所以Swift 3新增了两个修饰符对原来的private、public进行细分。

## 一、各个修饰符区别

### 1，private
`private`访问级别所修饰的属性或者方法只能在当前类里访问。

### 2，fileprivate
`fileprivate`访问级别所修饰的属性或者方法在当前的Swift源文件里可以访问。


<!--more-->


### 3，internal（默认访问级别，internal修饰符可写可不写）
`internal`访问级别所修饰的属性或方法在源代码所在的整个模块都可以访问。
如果是框架或者库代码，则在整个框架内部都可以访问，框架由外部代码所引用时，则不可以访问。
如果是App代码，也是在整个App代码，也是在整个App内部可以访问。

### 4，public
可以被任何人访问。但其他`module中`不可以被`override`和`继承`，而在module内可以被override和继承。

### 5，open
可以被任何人使用，包括override和继承。


## 二、5种修饰符访问权限排序
从高到低排序如下：
```
open > public > interal > fileprivate > private
```
