---
title: "使用composer 报错提示 Failed to decode response zlib_decode() data error"
categories: [ "PHP" ]
tags: [ "php","composer" ]
draft: false
slug: "3552"
date: "2017-11-16 16:05:00"
---

笔者在更新自己flarum论坛[破晓启航论坛](http://bbs.mbioq.com)的时候报的错误。如图
![php_degrade_mode_error.png][1]
```bash
[root@jdu4e00u53f7 html]# composer update
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Updating dependencies (including require-dev)

Failed to decode response: zlib_decode(): data error
Retrying with degraded mode, check https://getcomposer.org/doc/articles/troubleshooting.md#degraded-mode for more info
```


<!--more-->


网上有人说这个只要使用 `composer selfupdate` 就可以解决. 其实并不完全对.
因为我是再使用 `composer selfupdate` 这个时提示这个错误的.
其原因是因为权限不足造成的.我再 windows 下并没有使用 cmd 而是使用自己下载的 `Cmder`.
所以会有权限的问题.同理 linux 下如果权限不足也会出现这个错误.


  [1]: https://imgs.gnux.cn/usr/uploads/2017/11/3520854781.png