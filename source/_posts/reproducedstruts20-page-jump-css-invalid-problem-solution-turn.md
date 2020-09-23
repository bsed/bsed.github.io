---
title: [转载]Struts2.0页面跳转css无效问题的解决方法(转)
date: 2010-09-22 18:09:00
updated: 2015-09-20 18:10:52
tags: 
- github
categories: 
- linux

---
> 原文地址：Struts2.0页面跳转css无效问题的解决方法(转)作者：Easonchen

 
Struts2中跳转后，所有页面的CSS样式进入文件统统都失效了。。。

文件不是没有引入，是因为路径问题。。

有人说struts回来的页面就是路径少了一层。。比如你的jsp放在一个jsp包里面。。那转回来的。这一层就没有了。它当最外层了。。
可是我绝对路径 相对路径各种试都不行。。。

不明白用绝对路径为什么也不可以。。。

最简单的一招就是 利用重定向。。

<!--more-->

这样就OK 。。。

顺便查了一下struts2支持的一些结果类型，一并列在下面：

type支持的结果类型：

    chain:用来处理Action链 
    chart:整合JFreeChart的结果类型 
    dispatcher:用来转向页面，通常处理JSP 
    freemaker:处理FreeMarker模板 
    httpheader:控制特殊HTTP行为的结果类型 
    jasper:用于JasperReports整合的结果类型 
    jsf：JSF整合的结果类型 
    redirect:重定向到一个URL 
    redirect-action:重定向到一个Action 
    stream：向浏览器发送InputSream对象，通常用来处理文件下载，还可用于返回AJAX数据 
    tiles：与Tiles整合的结果类型 
    velocity：处理Velocity模板 
    xslt:处理XML/XLST模板 
    plaintext:显示原始文件内容，例如文件源代码

本文来自CSDN博客，转载请标明出处：http://blog.csdn.net/cxc3980/archive/2009/03/09/3973324.aspx