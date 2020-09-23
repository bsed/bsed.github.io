---
title: HTML元素之script的相关属性
date: 2013-08-05 16:40:00
updated: 2015-07-27 16:44:23
tags: 
- github
- wordpress
categories: 
- js

---
script表单元素在里面增加了一个HTML5的标签async，脚本可以通过异步运行，而不是阻塞型的。
| 属性 |	值 |	描述 |
| --- | --- | ---|
| async | async | 规定异步执行脚本（仅适用于外部脚本）。 |
| charset | charset | 规定在外部脚本文件中使用的字符编码。| 
| defer | defer | 规定是否对脚本执行进行延迟，直到页面加载为止。| 
| language | script | 不赞成使用。规定脚本语言。请使用 type 属性代替它。| 
| src | URL | 规定外部脚本文件的 URL。 | 
| xml:space |  	preserve | 规定是否保留代码中的空白。 | 


<!--more-->


`async`：异步的好处不言而喻，可以不用等待外部脚本运行完毕才继续运行下去，这样子在脚本运行中就不会因为位置而影响到执行速度了。

`charset`：当外部脚本的字符编码跟网页编码不一致时，你需要声明这个脚本使用的字符编码。charset="gb2312"。

`defer`：规定是否对页面进行延迟，直到加载完毕才执行。仅支持IE浏览器。defer="defer"