---
title: "Windows 下的 Git 设置Sublime Text3为默认编辑器"
categories: [ "Linux" ]
tags: [ "windows","Sublime Text3","git" ]
draft: false
slug: "git-under-the-sublime-set-text3-windows-as-the-default-editor"
date: "2014-10-17 12:32:00"
---

在Windows下Git的默认编辑器是vim。如果想换成其它的编辑器，比如 Sublime Text 2，可以使用如下方法：

    git config --global core.editor "'D:/Program Files/Sublime Text 3/sublime_text.exe' -w "

是的，上面写的没有错，需要为编辑器的路径单独添加引号，否则配置不会成功。