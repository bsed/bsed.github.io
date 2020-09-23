---
title: WordPress 嵌入 Github Gist 代码[转]
date: 2013-12-23 17:50:00
updated: 2015-07-30 17:52:05
tags: 
- css
- alpha
categories: 
- css

---
https://gist.github.com/ccbikai/11216526

其中 #https://gist.github.com/(.*?)/([\d]+)#i 需改成 #https://gist.github.com/(.*?)/([\w]+)#i

因为原 Gist 链接格式为： https://gist.github.com/ccbikai/11216526，


<!--more-->


现在是：https://gist.github.com/lmm214/5db1a1702a880baecba5。

原来最后部分都是数字，现在是字母+数字了。所以正则式稍作修改 [\d] 改为 [\w]

使用方法？把修改后的代码段丢入 functions.php 文章，另起一行链接丢入：

https://gist.github.com/lmm214/5db1a1702a880baecba5

https://gist.github.com/lmm214/5db1a1702a880baecba5

原文：[http://immmmm.com/wordpress-embedded-github-gist.html](http://immmmm.com/wordpress-embedded-github-gist.html)