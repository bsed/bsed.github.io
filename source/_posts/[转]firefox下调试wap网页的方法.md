---
title: "[转]firefox下调试wap网页的方法"
categories: [ "Css" ]
tags: [ "firefox","debug" ]
draft: false
slug: "method-of-debugging-the-wap-web-page-underfirefox"
date: "2011-09-25 09:38:00"
---

**做法：**

  1.下载所需的安装文件：

  a) Firefox: http://www.mozilla.com/firefox/

  b) User Agent Switcher Extension: http://chrispederick.com/work/useragentswitcher/

  c) Wmlbrowser Extension: http://wmlbrowser.mozdev.org/installation/wmlbrowser.html

  d) FireBug  https://addons.mozilla.org/en-US/firefox/addon/1843 此工具用来调试，get post 的请求跟相应信息。

  2.安装 Firefox Author: clapton_xpAThotmailDOTcom

  3.安装上述两个插件：在 Firefox 中 open 对应的两个 xpi 文件即安装。重启 Firefox 。

两个wap插件的下载地址如下：（通过firefox浏览器访问以下地址并下载安装）


<!--more-->


wmlbrowser 0.7.17
Simulate WAP browsing by viewing WML (Wireless Markup Language) pages in Mozilla, SeaMonkey or Mozilla Firefox.

WBMP images are not supported.
User Agent Switcher 0.6.11
Adds a menu and a toolbar button to switch the user agent of the browser.

  4 配置两个插件

  a) Wmlbrowser 无须配置自动生效，在请求 wml 页面时自动生效

  b) User Agent Switcher 在 Tools-> User Agent Switcher->Options->Options->User Agents 中可以添加修改删除所需的 UA 项。

  5. 添加 CMWAP 代理：跟 Opera 的做法类似

  a) Tools->Options->General->Connection->Connection Settings->Manual proxy configuration

  b) HTTP Proxy:10.0.0.172 Port:80

   6. 拨号至 CMWAP ，选择配置好的 UserAgent ，即可上网喽， www 和 wap 都可以。

   关于怎么进行CMWAP拨号，网络上的设置以及参考资料相当的多，有兴趣的可以查看下。

 

上面的组合完美无比，足可以完成所有的wap操作。包括移动的517网关拦截，订购页面的debug。

更新：：
https://developer.mozilla.org/zh-CN/docs/Tools/Remote_Debugging/Firefox_for_Android