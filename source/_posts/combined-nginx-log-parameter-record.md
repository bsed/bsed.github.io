---
title: Nginx combined 日志参数记录
date: 2016-11-08 21:32:00
updated: 2016-11-27 17:26:45
tags: 
- linux
- debian
categories: 
- linux

---
因为lnmp现在默认不自定义设置log格式，使用了combined默认格式，记录一下。
```bash
'$remote_addr - $remote_user  [$time_local]  '
' "$request"  $status  $body_bytes_sent  '
' "$http_referer"  "$http_user_agent" ';
```
参数解释：


<!--more-->


```
$remote_addr, $http_x_forwarded_for 记录客户端IP地址。
$remote_user 记录客户端用户名称。
$request 记录请求的URL和HTTP协议。
$status 记录请求状态。
$body_bytes_sent 发送给客户端的字节数，不包括响应头的大小。
$http_referer 记录从哪个页面链接访问过来的。
$http_user_agent 记录客户端浏览器相关信息。
$bytes_sent 发送给客户端的总字节数。
$connection 连接的序列号。
$connection_requests 当前通过一个连接获得的请求数量。
$msec 日志写入时间。单位为秒，精度是毫秒。
$pipe 如果请求是通过HTTP流水线(pipelined)发送，pipe值为“p”，否则为“.”。
$request_length 请求的长度（包括请求行，请求头和请求正文）。
$request_time 请求处理时间，单位为秒，精度毫秒。
$time_iso8601 ISO8601标准格式下的本地时间。
$time_local 通用日志格式下的本地时间。
```