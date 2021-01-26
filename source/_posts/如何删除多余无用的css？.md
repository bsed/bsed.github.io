---
title: "如何删除多余无用的css？"
categories: [ "Css" ]
tags: [  ]
draft: false
slug: "how-to-delete-the-redundant-css"
date: "2012-11-21 22:07:00"
---

项目经过几个版本的迭代，几个css文件加起来都有快6k行了，目测一半都是没用的代码，有没有自动的工具识别并删除这些代码？
网上搜了一些，要么就是不好用，要么就是只能搜索出没用的css，不能自动删除的。
有没有人有更好的办法？


<!--more-->


[cute网友](http://segmentfault.com/u/cute)

如果你的针对不同的浏览器兼容性的CSS比较少的话,可以使用Chrome的审计(Audits)功能(Remove unused CSS rules):

 1. 在Chrome浏览器里打开网页
 2. 快捷键Ctrl+Shit+i,打开开发面板.
 3. 点击run
 4. 如图(Remove unused CSS rules)

![chrome_audit_articlex.jpg][1]
[solodu网友](http://segmentfault.com/u/solodu)

Firefox有个插件 [Dust-me Selectors](https://addons.mozilla.org/en-US/firefox/addon/dust-me-selectors/)

其实是个挺复杂的事情，因为css的重载、复用和DOM的可变性，需要我们动态地分析。

另一个网友的回答：

我的想法是给chrome写个插件，然后对整个站点启用。

基本思路：

 1. 加载到需要测试的页面里，先扫描一遍所有的class和id。
 2. 监听DOMSubtreeModified事件，操作网页，充分测试，插件针对性地进行增补class和id。
 3. 重复以上步骤得到整个站点的列表
 4. 然后就是扫描css file把不没用上的的全去掉。

CSS Usage 用过吗？:D


  [1]: https://imgs.gnux.cn/usr/uploads/2015/01/342254963.jpg