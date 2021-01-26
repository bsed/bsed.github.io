---
title: "golang 中string rune byte 三者的关系[go学习笔记]"
categories: [ "Golang" ]
tags: [ "golang","学习笔记" ]
draft: false
slug: "string-rune-golang-byte-three-go-learning-notes"
date: "2013-10-31 18:06:00"
---

在Go当中 string底层是用byte数组存的，并且是不可以改变的。

例如 `s:="Go编程"` ,`fmt.Println(len(s))` 输出结果应该是8因为中文字符是用3个字节存的。

`len(string(rune('编')))`的结果是3

如果想要获得我们想要的情况的话，需要先转换为rune切片再使用内置的len函数

`fmt.Println(len([]rune(s)))`

结果就是4了。

所以用string存储unicode的话，如果有中文，按下标是访问不到的，因为你只能得到一个byte。 要想访问中文的话，还是要用rune切片，这样就能按下表访问。
