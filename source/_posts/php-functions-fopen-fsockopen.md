---
title: php中的内置函数 fopen / fsockopen 
date: 2013-05-15 16:36:00
updated: 2015-01-08 15:32:13
tags: 
- php
- json_encode
categories: 
- php

---
fopen 与 fsockopen 均可以打开一个URL地址，那么究竟有什么区别呢？

**第一、用法**

    Example. 
    Not
    
       $viart_xml = fopen("http://www.viart.com/viart_shop.xml", "r");
    
    But
    
       $viart_xml = fsockopen("www.viart.com", 80, $errno, $errstr, 12);
    
       fputs($viart_xml, "GET /viart_shop.xml HTTP/1.0\r\n");
       fputs($viart_xml, "Host: www.viart.com\r\n");
       fputs($viart_xml, "Referer: http://www.viart.com\r\n");
       fputs($viart_xml, "User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)\r\n\r\n"); 

从上面的代码可以看到，fopen的参数是 URL 地址，而fsockopen的参数是一个域名。


<!--more-->


**第二、实现**
fopen 
1. 更高级，对一些调用细节有一定的封装。
2. 可使用场合：http/ ftp / 本地文件
fsockopen
1. 更底层，基于端口来连接。
2. 可以实现与网络连接相关的所有功能，但是如果要打开一个普通的URL地址，fsockopen还需要指定端口，而fopen却可以根据http协议用默认的80端口，因此在这种情况下，fopen更方便。
3. 对于本地文件，fsockopen好像也是无能为力。