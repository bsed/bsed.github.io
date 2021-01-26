---
title: "Package cl is deprecated 警告 去除"
categories: [ "Vim" ]
tags: [ "emacs","cl" ]
draft: false
slug: "package-cl-is-cancelled-warning"
date: "2021-01-09 13:38:00"
---

emacs 每次启动时都会显示 Package cl is deprecated这很烦人。

在启动时隐藏
Emacs JP Slack教授的方法。


<!--more-->


```
;; (require 'cl) を見逃す
(setq byte-compile-warnings '(not cl-functions obsolete))
```
浏览时隐藏init.el

使用文件局部变量，
```
;; Local Variables:
;; byte-compile-warnings: (not cl-functions obsolete)
;; End:
```