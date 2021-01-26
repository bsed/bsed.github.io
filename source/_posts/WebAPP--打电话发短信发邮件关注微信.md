---
title: "WebAPP: 打电话发短信发邮件关注微信"
categories: [ "Css" ]
tags: [ "webapp","weixin" ]
draft: false
slug: "webapp-to-send-a-message-to-send-an-email-to-focus-on-micro-channel"
date: "2015-08-14 09:58:00"
---

现在web app还是比较流行的，刚用jquery mobile做了个通讯录的页面，需要直接点击电话链接直接给该电话拨打号码，发短信，发邮箱等，其实，html已经提供了这样的功能。这里暂且不考虑兼容性的问题。

实现如下：

    <a href="tel:10086">10086</a>
    <a href="sms:10086">短信：10086</a>
    <a href="weixin://addfriend/kuaipao8-com">微信号：kuaipao8-com</a>
    <a href="mailto:378588579@qq.com">电子邮箱：378588579@qq.com</a>

<a href="tel:10086">10086</a>
<a href="sms:10086">短信：10086</a>
<a href="weixin://addfriend/zzz">微信号：zzz</a>
<a href="mailto:zzz@qq.com">电子邮箱：zzz@qq.com</a>
