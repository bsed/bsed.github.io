---
title: "nginx防盗链设置"
categories: [ "Linux" ]
tags: [ "nginx" ]
draft: false
slug: "nginx-security-chain-settings"
date: "2013-11-18 08:51:00"
---

http协议通过referer表头字段来跟踪来源，我们可以通过检查referer来实现防盗链，不是从本站的请求返回404或指定的文件，常见的web服务器都支持防盗链。 Nginx 防盗链设置很简单，只需要将下面的配置加入nginx.conf中

    location ~* \.(ico|gif|bmp|jpg|jpeg|png|swf|js|css|mp3) {
            root   /home/html/52os;
            expires 30d;  #静态文件缓存过期时间
            valid_referers none blocked www.xx.net xx.net;
            if ($invalid_referer) {
            return 404;
        }
        }

配置说明：

用 (“|”) 来分隔你想保护的文件的扩展名。
valid_referers 指令包含允许访问资源的网站列表,不在列表中请求的返回404。
none blocked 最后的网站就是允许访问的网站，用空格分开，可以用*来匹配所有二级域名。


<!--more-->


还可以使用location对特定目录进行限制。例如保护images文件夹location /images/
检查配置文件是否有错误: /nginx/sbin/nginx -t，如果检查通过重启nginx即可。

测试防盗链是否生效：

假设图片的路径为 http://www.xxx.net/a.jpg,用下面两条命令测试:

    curl -I http://www.xx.net/a.jpg  -H "referer:http://www.xx.net/"
    curl -I http://www.xx.net/a.jpg  -H "referer:http://xx.net/"
    
    HTTP/1.1 200 OK
    Server: Tengine/1.5.0
    Date: Tue, 13 Aug 2013 07:14:19 GMT
    Content-Type: image/png
    Content-Length: 2034
    Last-Modified: Thu, 01 Aug 2013 12:50:05 GMT
    Connection: keep-alive
    Expires: Thu, 12 Sep 2013 07:14:19 GMT
    Cache-Control: max-age=2592000
    Accept-Ranges: bytes

如果出现上面的HTTP 200提示说明从52os.net访问是没有问题的，在试试从其它网站访问

    curl -I http://www.xx.net/a.jpg  -H "referer:http://www.x.net/"
    HTTP/1.1 404 Not Found
    Server: Tengine/1.5.0
    Date: Tue, 13 Aug 2013 07:14:26 GMT
    Content-Type: text/html
    Content-Length: 633
    Connection: keep-alive

从其它网站访问返回404,说明防盗链设置成功了
