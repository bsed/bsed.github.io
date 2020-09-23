---
title: Http请求报文_HttpRequest 
date: 2010-08-21 11:25:00
updated: 2016-06-02 11:22:05
tags: 
- java
- Apache
- commons
categories: 
- java

---
    //HttpRequest封装了请求的方法，uri，和请求行，还有请求头信息
    HttpRequest request = new BasicHttpRequest("GET", "/", HttpVersion.HTTP_1_1);
    //请求行：http请求信息报文的第一行
    RequestLine requestLine = request.getRequestLine();
    System.out.println("请求的行的信息：" + requestLine.toString());		
    System.out.println("获得请求方法：" + requestLine.getMethod());
    System.out.println("获得请求uri：" + requestLine.getUri());
    System.out.println("获得Http协议版本号：" + requestLine.getProtocolVersion());


<!--more-->


*打印结果：*


    请求的行的信息：GET / HTTP/1.1
    获得请求方法：GET
    获得请求uri：/
    获得Http协议版本号：HTTP/1.1


这是第一篇主要是了解一下HttpRequest这个接口的意义

这个类还有其他的方法,获得请求头等信息的方法，获得请求参数等，如下图

图片丢失:>