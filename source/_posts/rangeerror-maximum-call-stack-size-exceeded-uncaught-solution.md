---
title: Uncaught RangeError: Maximum call stack size exceeded解决方法
date: 2015-07-22 15:16:00
updated: 2015-08-03 16:35:26
tags: 
- jquery
- scroll
categories: 
- java

---
今天突然碰到这样的JavaScript错误：`Uncaught RangeError: Maximum call stack size exceeded`
如图所示：
[![20150803141526.png][1]](https://imgs.gnux.cn/usr/uploads/2015/08/435405757.png)
错误原因就是堆栈溢出了。

1、原因：有小类到大类的递归查询导致溢出

2、解决方法思想：

A、在做递归查询时候由大类到小类去查询


<!--more-->


B、匹配结果后及时return 退出，防止过多查询


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/435405757.png