---
title: "配置 Tomcat Access Log 的方法"
categories: [ "Java" ]
tags: [ "tomcat" ]
draft: false
slug: "method-for-configuring-access-log-tomcat"
date: "2013-11-18 07:57:00"
---

## 配置方法，在conf/server.xml中打开（默认关闭的)

    <Valve className="org.apache.catalina.valves.AccessLogValve">
    prefix="localhost_access_log." suffix=".log"
    pattern=" common " directory="${jboss.server.log.dir}"
    resolveHosts="false"/>

最重要的参数：
common的值：%h %l %u %t %r %s %b
combined的值：%h %l %u %t %r %s %b %{Referer}i %{User-Agent}i
填写 common,日志中将包含下面信息：

`访问者IP 访问时间 访问方式以及uri 返回状态 反应时间`

192.168.1.102 - - [13/Dec/2011:17:14:33 +0800] "POST /member/isEmailExist HTTP/1.1" 200 84
192.168.1.102 - - [13/Dec/2011:17:40:56 +0800] "POST /member/decode HTTP/1.1" 404 -
192.168.1.102 - - [13/Dec/2011:17:41:30 +0800] "POST /ToolsWS/common/decode HTTP/1.1" 200 71

如果填写 combined ,将有referer和ua信息,如下：

    192.168.1.102 - - [13/Dec/2011:18:01:46 +0800] "GET /favicon.ico HTTP/1.1" 404 47 "-" "Mozilla/5.0 (X11; U; Linux x86_64; zh-CN; rv:1.9.2.24) Gecko/20111107 Ubuntu/10.04 (lucid) Firefox/3.6.24" 
    192.168.1.102 - - [13/Dec/2011:18:02:09 +0800] "GET /admin-console/index.seam HTTP/1.1" 200 1821 "-" "Mozilla/5.0 (X11; U; Linux x86_64; zh-CN; rv:1.9.2.24) Gecko/20111107 Ubuntu/10.04 (lucid) Firefox/3.6.24"
    192.168.1.102 - - [13/Dec/2011:18:02:09 +0800] "GET /admin-console/a4j/s/3_3_3.Finalorg/richfaces/renderkit/html/css/basic_classes.xcss/DATB/eAHTj7jOHbp8hjQADb0DGQ__ HTTP/1.1" 200 7126 "http://192.168.1.53:8080/admin-console/index.seam" "Mozilla/5.0 (X11; U; Linux x86_64; zh-CN; rv:1.9.2.24) Gecko/20111107 Ubuntu/10.04 (lucid) Firefox/3.6.24"

| %a | 访问者的IP |
| --- | --  |
| %A | 本地服务器的IP
| %b | 发送的字节数，不包括http头，如果字节数为0的话，显示为- |
| %B | 发送的字节数，不包括http头.  |
| %h | 访问者主机名，如果resolveHosts为false的话，这里就是IP地址了 |
| %H | 访问者使用的协议，如：HTTP/1.1 |
| %l | always return '-' ，从identd返回的访问者逻辑用户名 |
| %m | 访问的方式，是GET还是POST |
| %p | 本地接收访问的端口 |
| %q | 查询字符串，比如你访问的是aaa.jsp?bbb=ccc，那么这里就显示?bbb=ccc |
| %r | 请求的第一行，包含请求方法和URI |
| %s | 响应的状态码 |
| %S | 用户的session  ID |
| %t | 日志和时间，使用通常的Log格式 |
| %u | 认证以后的远端用户，不存在就是"-" |
| %U | 访问的URI路径 |
| %v | 本地服务器名称 |
| %D | 处理请求的时间，以毫秒记 |
| %T | 处理请求的时间，以秒记 |

*eg:配置*

    pattern="192.168.1.54: %a %A %b %B %h %H %l %m %p %q %r %s %S %t %u %U %v %D %T %{Referer}i %{User-Agent}i %{X-Forwarded-For-Pound}i %{HTTP_X_UP_CALLING_LINE_ID}i %{x-up-calling-line-id}i " 

*访问：*

    http://192.168.1.54:8080/ToolsWS

日志将记录：

    192.168.1.54: 192.168.1.102 127.0.0.1 367 367 192.168.1.102 HTTP/1.1 - GET 8080 ?stylesheet=1 GET /ToolsWS/?stylesheet=1 HTTP/1.1 200 - [19/Jan/2012:15:37:57 +0800] - /ToolsWS/ 192.168.1.54 110.011 http://192.168.1.54:8080/ToolsWS Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/535.7 (KHTML, like Gecko) Chrome/16.0.912.63 Safari/535.7 - - -

推荐使用：

    pattern="%a %b %t %u %U %v %D %T %{Referer}i %{User-Agent}i " 

