---
title: "Zimbu中的Unix echo 的例子"
categories: [ "Zimbu" ]
tags: [ "echo" ]
draft: false
slug: "unix-echo-example"
date: "2015-01-09 14:16:00"
---

Unix风格的在“echo”程序例子：

    # Zimbu example program: Unix style "echo"
     FUNC Main() int
       bool    writeNewline = TRUE
       string  sep = ""
       bool    didFirst
       FOR arg IN ARG.getRawList()
         IF !didFirst && arg == "-n"
           writeNewline = FALSE
         ELSE
           IO.write(sep .. arg)
           sep = " "
         }
         didFirst = TRUE
       }
       IF writeNewline
         IO.write("\n")
       }
       RETURN 0
     }
     ARG.Status argStatus = ARG.disable()

让我们来一行一行的解释：
 

> # Zimbu example program: Unix style "echo"

注释是以`#`开始，直到行的结尾。

> FUNC Main() int

程序的入口点。没有参数在这里。命令行参数的参数模块处理随处可见。这避免了通过它们创造的依赖。main()总是返回一个int。

>  bool    writeNewline = TRUE

声明一个布尔型变量writenewline并初始化为true，zimbu是静态类型的语言，这意味着编译器将检查writenewline将永远作为一个布尔类型，类型检查正确后才会运行程序。

> string  sep = ""

声明一个变量SEP（简称分隔符(separator)）为字符串型，将它初始化为空字符串。

>   bool    didFirst

声明一个布尔型变量didfirst。没有显式初始化，则使用默认值FALSE。所有的变量都默认初始化为零(zero)，错误(FALSE )或无(NIL)。

>  FOR arg IN ARG.getRawList()

这是一个FOR IN 迭代语句。遍历列表中的所有元素(getRawList()
)，分配每个元素的值传给arg。就像Python。

在这种情况下，通过ARG.getRawList()获取了一个列表，列表中包含了所有的命令行参数,ARG Module实际上具有良好的应对方式标志，但由于echo命令是关于如何处理争论我们需要避免，例如非常具体的，`--help`。

arg是一个循环变量。不需要声明，它是自动出现的。这是一个很小的地方你可以使用一个变量没有声明它。arg的范围是只在这个循环中出现。zimbu还允许使用一个声明的变量。
变量的类型是来源于迭代列表。因此，它是静态类型的，但我们不需要明确的声明这个类型。在这种情况下，类型的返回值来源于ARG.getRawList()，一个字符串列表。

>  IF !didFirst && arg == "-n"

一个条件语句。在表达中括号`()`不是必需的。表达式语法类似于C和Java。但是字符串值可以直接比较`= =`。比较对象可以用`IS`，有点像Python

> writeNewline = FALSE

`-N`的说法被发现，复位`writeNewline` 从而省略最后的换行。

>   ELSE

和其他语言一样

> IO.write(sep .. arg)

..运算符用于连接字符串。

 sep = " "

在一个参数前面加上空格
 
> }

if语句的结束。是的，有不平衡括号zimbu。你可以看到它作为结束，如Pascal。该代码是容易的概述，而不是单词结束的字符}。

>  didFirst = TRUE

在我们不再是在第一个参数。下一次我们将不检查`-N`的说法。这是与UNIX命令：`echo -n -n hello`写`-n hello`。

> }

FOR 循环的结束

> IF writeNewline
>      IO.write("\n")   
> }

当writeNewline写入新行。

> RETURN 0 
> }

main()功能块结束。当返回0的时候，程序退出。

>  ARG.Status argStatus = ARG.disable()

告诉ARG Module 没用arg命令行参数被完成。声明一个变量并且初始化在，命令行参数被替换的时候。将arg的状态改为disable