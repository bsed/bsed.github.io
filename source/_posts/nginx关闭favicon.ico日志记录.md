---
title: "nginx关闭favicon.ico日志记录"
categories: [ "Linux" ]
tags: [ "favicon" ]
draft: false
slug: "nginx-to-close-the-faviconico-log-records"
date: "2012-08-13 13:39:00"
---

#把以下配置放到 server {} 块.
 
#关闭favicon.ico不存在时记录日志

    location = /favicon.ico {
    log_not_found off;
    access_log off;
    }