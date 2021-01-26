---
title: "Access-Control-Allow-Origin 解决跨域权限问题"
categories: [ "Linux" ]
tags: [ "nginx","权限" ]
draft: false
slug: "accesscontrolalloworigin-to-solve-the-problem-of-cross-domain-permissions"
date: "2015-08-10 10:38:00"
---

> 解决跨域权限问题

今天设置静态资源服务器时发现

    Font from origin 'http://status.yigrherb.com' has been blocked from loading by Cross-Origin Resource Sharing policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://lib.yigrherb.com' is therefore not allowed access. 

这里我给静态资源单独设置了一个域名，其它页面调用时发生了跨越权限问题。


<!--more-->


Nginx解决办法：
```
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Headers X-Requested-With;
add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
```
将这段代码添加到` http{}` 或者静态资源对应的 `server{} `中

如果只为自己的网站使用可以将第一行代码的星号改为自己的域名，例如`*.yigrherb.com`

参考资料
[http://www.w3.org/TR/cors/#access-control-allow-methods-response-header](http://www.w3.org/TR/cors/#access-control-allow-methods-response-header)
