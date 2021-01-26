---
title: "VS CODE配置运行PHP代码"
categories: [ "PHP" ]
tags: [ "php","vscode" ]
draft: false
slug: "code-vs-configuration-running-php-code"
date: "2016-05-25 09:56:00"
---

## 简介

距离微软发布VS CODE已经有几个月的时间了,我也下载试用了一段时间.发现这个编辑器还是不错的.
不过由于是第一版,很多地方还不是很完善,说明也很有限.多语言及插件化还没有完成.
对于我来说,是希望写完代码直接可以运行的,所以经过一番尝试,找到了VS CODE集成其他外部工具运行代码的方法.
特此记录.
## 教程

打开VS CODE的命令操作台(`ctrl+shift+p`),输入 `configure task runner` ,打开`tasks.json`.
设置其中的运行配置如下:


<!--more-->


```
{
"version": "0.1.0",
"command": "C:\\xampp\\php\\php.exe",
"isShellCommand": true,
"showOutput": "silent",
"tasks": [
    {
        "taskName": "test",
        "showOutput": "silent"
    }
],
"args": ["${file}"]
}
```
此时在需要运行的PHP页面打开控制台,输入 `Run Task` 运行,然后选中 `test`
或者直接输入 `Run Test Task` 运行(快捷键 `ctrl+shift+t`)
均可直接运行当前PHP文件.其他语言代码同理.

## 总结

VS CODE作为微软尝试跨平台编辑器的首个作品,其表现还是令人满意的.
不过由于是初版,还有很大的进步空间,比如PHP无法进行定义跳转(Node.js可以),配置外部工具比较隐蔽等等.
但是有理由相信经过版本的更新,VS CODE会是一个可以作为主力的编辑器.

原文：[http://codeape.leanote.com/post/Untitled-55192a6538f41114e800035c-12](http://codeape.leanote.com/post/Untitled-55192a6538f41114e800035c-12)