---
title: "[转载]PHP设置Cookie的HTTPonly属性"
categories: [ "PHP" ]
tags: [ "php","httponly","cookie" ]
draft: false
slug: "php-set-cookie-httponly-properties"
date: "2013-10-22 14:41:00"
---

原文链接: [PHP设置Cookie的HTTPonly属性](http://blog.sina.com.cn/s/blog_582246d20100jkz8.html)

PS: 直接转载, 有时间再整理下排版, 有点挤...


<!--more-->


httponly是微软对cookie做的扩展。这个主要是解决用户的cookie可能被盗用的问题。
大家都知道，当我们去邮箱或者论坛登陆后，服务器会写一些cookie到我们的浏览器，当下次再访问其他页面时，由于浏览器回自动传递cookie，这样就实现了一次登陆就可以看到所有需要登陆后才能看到的内容。也就是说，实质上，所有的登陆状态这些都是建立在cookie上的！假设我们登陆后的cookie被人获得，那就会有暴露个人信息的危险！当然，想想，其他人怎么可以获得客户的cookie？那必然是有不怀好意的人的程序在浏览器里运行！如果是现在满天飞的流氓软件，那没有办法，httponly也不是用来解决这种情况的，它是用来解决浏览器里javascript访问cookie的问题。试想，一个flash程序在你的浏览器里运行，就可以获得你的cookie的！

IE6的SP1里就带了对httponly的支持，所以相对还说还是些安全性。

PHP中的设置 
PHP5.2以上版本已支持HttpOnly参数的设置，同样也支持全局的HttpOnly的设置，在php.ini中
----------------------------------------------------- 
session.cookie_httponly = 
----------------------------------------------------- 
设置其值为1或者TRUE，来开启全局的Cookie的HttpOnly属性，当然也支持在代码中来开启： 
----------------------------------------------------- 
// or session_set_cookie_params(0, NULL, NULL, NULL, TRUE); 
?> 
----------------------------------------------------- 
Cookie操作函数setcookie函数和setrawcookie函数也专门添加了第7个参数来做为HttpOnly的选项，开启方法为： 
------------------------------------------------------- 
setcookie("abc", "test", NULL, NULL, NULL, NULL, TRUE); 
setrawcookie("abc", "test", NULL, NULL, NULL, NULL, TRUE);
------------------------------------------------------- 
对于PHP5.1以前版本以及PHP4版本的话，则需要通过header函数来变通下了： 
------------------------------------------------------------- 

-------------------------------------------------------------