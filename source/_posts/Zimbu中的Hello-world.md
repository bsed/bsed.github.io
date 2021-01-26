---
title: "Zimbu中的Hello world"
categories: [ "Zimbu" ]
tags: [ "zimbu","helloworld" ]
draft: false
slug: "in-zimbu-hello-world"
date: "2015-01-09 13:54:00"
---

####  Hello World 程序分析： ####

    FUNC Main() int
      IO.write("Hello, World!\n")
      RETURN 0
    }

####  分析：  ####


<!--more-->

 1. 程序的入口点是`main()`函数。
 2. 函数的返回值是一个`int`，`integer`。
 3. 关键词(`Keywords`)是大写字母，这避免了你需要知道的所有关键词当选择一个名字的问题。添加关键词后不会破坏任何现有zimbu程序。
 4. IO模块包含I/O的东西。`IO.write()`是一个标准的`stdout`，是`IO.stdout.write()`的简写。
 5. IO模块是语言的一部分，不需要导入，我们已经知道它在哪里。
 6. “\n”是一个换行符。字符串转义字符使用像C和Java。还有IO.writeline()追加一个换行符。
 7. 没有分号结束语句。
 8. main()的函数返回一零。这相当于`exit(0)`。
 9. `}`字符被用来结束一块。没有`{`，我们知道块开始。这避免了无用的讨论在哪里放{。