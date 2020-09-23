---
title: node.js 使用ejs模板引擎时，将模板页后缀换成.html
date: 2014-08-28 22:09:00
updated: 2014-09-11 22:08:06
tags: 
- js
- tab-js
categories: 
- js

---
这是一个小技巧，看着.ejs的后缀总觉得不爽，使用如下方法，可以将模板文件的后缀换成我们习惯的.html。

1.在app.js的头上定义ejs:
>var ejs = require('ejs');


<!--more-->


2.注册html模板引擎：
>app.engine('html',ejs.__express);

3.将模板引擎换成html:
>app.set('view engine', 'html');

4.修改模板文件的后缀为.html。

好了，任务完成，可以运行观看效果了！