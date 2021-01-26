---
title: "Windows下Git Bash中文乱码"
categories: [ "Linux" ]
tags: [ "windows","git" ]
draft: false
slug: "git-bash-chinese-garbled-windows"
date: "2013-02-11 11:13:00"
---

打开`Git Bash`
进入目录：`$ cd /etc`
 
## 1. 编辑 `gitconfig` 文件：`$ vi gitconfig`
文件中增加内容
*Xml代码:*

    [gui]  
    encoding = utf-8 #代码库统一使用utf-8  
    [i18n]  
    commitencoding = GB2312 #log编码，window下默认gb2312,声明后发到服务器才不会乱码  
    [svn]  
    pathnameencoding = GB2312 #支持中文路径  

<!--more-->


## 2. 编辑 `git-completion.bash` 文件：`$ vi git-completion.bash`
    最下面添加
*Xml代码:*

    alias ls='ls --show-control-chars --color=auto' #ls能够正常显示中文  

 
## 3. 编辑 inputrc 文件：
`$ vi inputrc`
修改 output-meta 和 convert-meta 属性值
*Xml代码:*

    set output-meta on #bash中可以正常输入中文  
    set convert-meta off  

 
## 4. 编辑 `profile` 文件：`$ vi profile`
最下面添加
*Xml代码:*

    export LESSHARSET=utf-8  

 
## 5. 重新编译配置文件，使文件生效

    $ source git-completion.bash
    $ source inputrc
    $ source profile


http://blog.csdn.net/self001/article/details/7337182