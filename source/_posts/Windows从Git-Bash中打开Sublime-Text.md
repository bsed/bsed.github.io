---
title: "Windows从Git Bash中打开Sublime Text"
categories: [ "JS" ]
tags: [ "Sublime Text3","bash","git","windiows" ]
draft: false
slug: "sublime-from-bash-git-to-open-text-windows"
date: "2015-10-17 12:56:00"
---

新建一个没有扩展名的文件 `subl` 填入如下内容,Sublime Text 的路径 改为自己的 我的路径是 ：`D:\Program Files\Sublime Text 3\sublime_text.exe`

    #!/bin/sh
    "D:\Program Files\Sublime Text 3\sublime_text.exe" $1 &

Copy 新建的subl 文件到 `C:\Program Files (x86)\Git\bin` 文件夹下.
`$1` 处理任何请求参数， 你可以打开Git 的Bash 控制台， 运行 如下命令：

    subl text.txt

或者：

    subl .

打开当前文件夹

最后一个参数，表明它应该在新的窗口中打开Sublime Text，这样你就可以继续使用命令提示。