---
title: "调试 Uncaught Error: [$injector:modulerr] [AngularJS]"
categories: [ "JS" ]
tags: [ "js","angularjs","debug" ]
draft: false
slug: "uncaught-error-injectormodulerr-in-angularjs-debugging"
date: "2015-07-14 14:14:00"
---

在当前AngularJS项目在工作中我遇到以下错误后该如何调试呢？   

     Uncaught Error: [$injector:modulerr] Failed to instantiate module app due to:
        Error: [$injector:modulerr] Failed to instantiate module app due to:
        Error: [$injector:modulerr] Failed to instantiate module app.common due to:
        Error: [$injector:moduler......1)

# 原因：

有些包是没有找到，或已被移动/重命名。在我的错误中，一些代码已经被移动，他们的引用包已经更新，然后我的代码抛出错误了。


<!--more-->


# 解决方法：

1.打开你的 angularjs.js 文件
2.定位到 ，你将看到下面的信息，（依赖于你所实用的版本）

     message += '\nhttp://errors.angularjs.org/1.4.0/' +
          (module ? module + '/' : '') + code;

3.在上面代码的后面，追加下面一句话：
`console.log(message);`
4.刷新你的应用，查看浏览器的控制台（console）信息，在日志里，将会给一些有用的错误提示（不正确的引用）。
5.在你的源代码里`search/replace` 不正确的引用，并修改正确，再次刷新应用程序，看问题是否解决了
 