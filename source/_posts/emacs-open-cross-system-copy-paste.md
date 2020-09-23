---
title: Emacs开启跨系统复制粘贴
date: 2010-11-02 16:14:00
updated: 2015-11-14 16:16:25
tags: 
- linux
- df
- du
categories: 
- linux

---
    META-X set-variable RET x-select-enable-clipboard RET t

或者 在.emacs上添加

    (setq x-select-enable-clipboard t)