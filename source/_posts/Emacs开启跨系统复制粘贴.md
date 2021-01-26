---
title: "Emacs开启跨系统复制粘贴"
categories: [ "Linux" ]
tags: [ "emacs","paste" ]
draft: false
slug: "emacs-open-cross-system-copy-paste"
date: "2010-11-02 16:14:00"
---

    META-X set-variable RET x-select-enable-clipboard RET t

或者 在.emacs上添加

    (setq x-select-enable-clipboard t)