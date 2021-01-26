---
title: "URL file-access is disabled in the server configuration 解决方案"
categories: [ "PHP" ]
tags: [ "php","allow_url_fopen" ]
draft: false
slug: "url-file-access-is-disabled-in-the-server-configuration-solution"
date: "2015-01-09 13:10:00"
---

在VPS上启用 PHP.ini 上设置 allow_url_fopen 和 allow_url_include 选项。
```ini
# vi /usr/local/php/etc/php.ini

;;;;;;;;;;;;;;;;;;;;;;;;;;;
; Fopen wrappers ;
;;;;;;;;;;;;;;;;;;;;;;;;;;;

; Whether to allow the treatment of URLs (like http:// or ftp://) as files.
allow_url_fopen = On

; Whether to allow include/require to open URLs (like http:// or ftp://) as files.
allow_url_include = On

```


<!--more-->


将 allow_url_fopen 和 allow_url_include 两个函数默认的'Off'改为'On'即可。