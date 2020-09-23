---
title: WSGI、flup、fastcgi、web.py的关系
date: 2015-02-05 10:58:00
updated: 2015-02-07 10:59:21
tags: 
- nginx
- cygwin
categories: 
- linux

---
`Apache/lighttpd`: 相当于一个request proxy，根据配置，把不同的请求转发给不同的server处理，例如静态的文件请求自己处理，这个时候它就像一个web server，对于fastcgi/python这样的请求转发给flup这样的Server/Gateway进行处理

 `flup`: 一个用python写的web server，也就是cgi中所谓的Server/Gateway，它负责接受apache/lighttpd转发的请求，并调用你写的程序 (application)，并将application处理的结果返回到apache/lighttpd

`fastcgi`: apache/lighttpd的一个模块，虽然flup可以作为一个独立的web server使用，但是对于浏览器请求处理一般都交给 apache/lighttpd处理，然后由apache/lighttpd转发给flup处理，这样就需要一个东西来把apache/lighttpd跟flup联系起来，这个东西就是fastcgi，它通过环境变量以及socket将客户端请求的信息传送给flup并接收flup返回的结果


<!--more-->


`web.py`: 应该说有了上面的东西你就可以开始编写你的web程序了，但是问题是你就要自己处理浏览器的输入输出，还有cookie、session、模板等各种各样的问题了，web.py的作用就是帮你把这些工作都做好了，它就是所谓的web framework，另外一个出名的是django，不过感觉太复杂了，web.py差不多就够用了

`WSGI` : 除了flup Server/Gateway外还有很多其他人的写的Server/Gateway, 这个时候就会出问题了，如果你在flup上写了一个程序，现在由于各种原因你要使用xdly了，这个时候你的程序也许就要做很多痛苦的修改才能使用 xdly server了，WSGI就是一个规范，他规范了flup这个服务应该怎么写，应该使用什么方式什么参数调用你写的程序(application)等，当然同时也规范你的程序应该怎么写了，这样的话，只要flup跟xdly都遵守WSGI的话，你的程序在两个上面都可以使用了，flup就是一个WSGI server