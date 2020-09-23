---
title: linux下的高效代码搜索工具-ack
date: 2015-09-21 11:11:00
updated: 2015-09-21 11:11:55
tags: 
- python
- list
categories: 
- python

---
 1. http://betterthangrep.com
 2. ack is a tool like grep, optimized for programmers
 3. is written purely in Perl 5,takes advantage of the power of
 4. Perl's regular expressions.

作者在厌烦了不停的写下面的这个查找命令之后，开发了ack这个工具

    grep foo $(find . -name '*.pm' | grep -v .svn)

可以看的出来，ack诞生的目的就是要取代grep，从作者开发的初衷以及它官网的名字，另外它还有一个“可以替代99%grep的工作”这个口号。
## 安装


<!--more-->


ubuntu下要安装ack-grep，因为在debian系中，ack这个名字被其他的软件占用了。` sudo apt-get install ack-grep`

## 特点

大家都说自己的东西好，因此ack官网列出了这工具的5大卖点：

 1. 速度非常快,因为它只搜索有意义的东西。
 2. 更友好的搜索，忽略那些不是你源码的东西。
 3. 为源代码搜索而设计，用更少的击键完成任务。
 4. 非常轻便，移植性好。
 5. 免费且开源

先来看下grep的日常用法。

*grep常用操作*

    grep -r 'hello_world' # 简单用法
    grep '^hello_world' . # 简单正则
    ls -l | grep .py # 管道用法

*一些参数：*

    -c(统记)/ -i(忽略大小)/ -h(不显示名称)/
    -l(只显文件名)/ -n(加行号)/ -v(显示不匹配)

这些命令在linux上的适用频率是相当高的，尤其是你用vim做为IDE的话（当然这是说在不知道ack之前）。

## ack功能划分

在记忆的时候大体上可以分为这几个部分：

 1. Searching代码搜索
 2. Search output搜索结果处理
 3. File presentation文件展示
 4. File finding文件查找
 5. File inclusion/exclusion文件过滤

下面对每一项给几个简单实用的例子。

## Searching

简单的文本搜索，默认是递归的。

    ack-grep hello
    ack-grep -i hello
    ack-grep -v hello
    ack-grep -w hello
    ack-grep -Q 'hello*'

## Search File

对搜索结果进行处理，比如只显示一个文件的一个匹配项，或者xxx

    ack-grep --line=1     # 输出所有文件第二行
    ack-grep -l 'hello'     # 包含的文件名
    ack-grep -L 'print'     # 非包含文件名

## File presentation

输出的结果是以什么方式展示呢，这个部分有几个参数可以练习下

    ack-grep hello --pager='less -R'    # 以less形式展示
    ack-grep hello --noheading      # 不在头上显示文件
    ack-grep hello --nocolor        # 不对匹配字符着色

## File finding

没错，它可以查找文件，以省去你要不断的结合find和grep的麻烦，虽然在linux的思想是一个工具做好一件事。

    ack-grep -f hello.py    # 查找全匹配文件
    ack-grep -g hello.py$    # 查找正则匹配文件
    ack-grep -g hello  --sort-files     #查找然后排序

## File Inclusion/Exclusion

文件过滤，个人觉得这是一个很不错的功能。如果你曾经在搜索项目源码是不小心命中日志中的某个关键字的话，你会觉得这个有用。

    ack-grep --python hello #查找所有python文件
    ack-grep -G hello.py$ hello     # 查找匹配正则的文件

## ack配置

也是一个相当赞的功能，对grep了解不多，不知道grep有没有同样的东西。通过配置可以把你的个人习惯做为默认配置，比如我是python程序员，那默认我要搜索的文件大多数必然是.py的文件。每次搜索时都要输入:--python那就太无聊了。

另外还可以自己指定类型，通过--type-set=conf=.conf,指定一个.conf的文件形式，ack默认提供常见源码的支持。你可以通过:ack-grep --help types查看它支持多少中类型的源码，绝对有你不知道的语言。

下面是一个简单的配置

    # 设置排序
    --sort-files
    #设置文件过滤
    --python
    --html
    --js
    --conf
    # 设置显示
    --noheading
    # 定义新的文件类型
    --type-set=conf=.conf
    # 智能识别大小写
    --smart-case
    # 设置以less形式展示，设定less参数
    --pager=less -R -M --shift 5 -i

**一些资源:**

官网文档
[ack作者采访](http://betterthangrep.com/documentation/)
[ack-grep和grep对比](http://blueparen.com/node/5)
它还有对应的vim插件，使用方法和在终端上一样。自己到git上去找吧。


