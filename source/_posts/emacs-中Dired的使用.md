---
title: "emacs 中Dired的使用"
categories: [ "Linux" ]
tags: [ "linux","emacs" ]
draft: false
slug: "the-use-of-dired-in-emacs"
date: "2012-09-06 21:28:00"
---

进入Dired

    C-x d         #Dired

Dired命令


<!--more-->


    M-x auto-compression-mode    #自动压缩解压缩模式，方便修改压缩文件
    M-x font-lock-mode        #开启或关闭彩色显示字体
    s         #切换到以日期排序
    f e Enter     #编辑当前行所在的文件
    #            #标记所有自动保存的文件为删除
    ~         #标记所有备份文件为删除
    D          #立即删除当前文件
    C          #复制文件
    num C          #复制当前文件及下面的多个文件
    R            #重命名
    %l         #将文件名改为小写
    %u         #将文件名改为大写
    Z          #压缩
    =         #比较两个文件
    M-=          #与自己的备份文件比较
    !          #在文件上执行Shell Command
    *          #代指当前文件名
    ?          #指分别对每一个文件执行Shell Command
    g          #刷新Dired
    M-Del          #去除所有mark
    t         #反选所有mark
    **          #选择所有可执行文件
    *@          #选择所有符号链接
    %m         #正则表达式选择
    %g          #grep搜索内容
    +           #新建一个文件夹
    Q         #query replace
    A         #多文件查找 (M-,可以移动到下一个匹配位置)
    ^         #移动到上一级目录
    >         #移动到下一个目录
    <         #移动到上一个目录
    i         #将子目录的内容附到buffer下面

Dired的View模式下

    s         #增量搜索
    =         #显示光标所在行号
    q          #退出

