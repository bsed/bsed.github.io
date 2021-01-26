---
title: "htaccess 将HTTP访问重定向至HTTPS"
categories: [ "Linux" ]
tags: [ "htaccess" ]
draft: false
slug: "http-will-access-the-htaccess-to-https"
date: "2012-06-30 10:12:00"
---

　非常实用的一个.htaccess规则，使用只需将下面代码另存为.htaccess文件，传到网站根目录下即可。当然前提是你已经开通了.htaccess支持。

将 http 访问强制重定向至 https，代码如下：

    RewriteEngine on
    RewriteBase /
    RewriteCond %{SERVER_PORT} !^443$
    RewriteRule ^.*$ https://%{SERVER_NAME}%{REQUEST_URI} [L,R=301]


<!--more-->


将 https 访问强制重定向至 http，代码如下：

    RewriteEngine on
    RewriteBase /
    RewriteCond %{SERVER_PORT} !^80$
    RewriteRule ^.*$ http://%{SERVER_NAME}%{REQUEST_URI} [L,R=301]

　　以上修改的.htaccess规则最后一行里直接包含了301永久性转移，如只需要302临时性转移，请将规则最后一行R=301中的=301删除即可。但302临时性转移对搜索引擎不友好。而添加301永久性转移后，就变成了永久性转移----传递原来的权重。