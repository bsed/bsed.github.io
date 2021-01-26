---
title: "windows 下 配置 symbols(符号)"
categories: [ "日常" ]
tags: [ "Symbols" ]
draft: false
slug: "configure-symbols-under-windows"
date: "2019-04-18 13:06:00"
---

操作方法:桌面我的电脑点右键--属性--高级--环境变量 ,点击新建。

方法一：
新建一个环境变量`_NT_SYMBOL_PATH 值为`: `SRV*c:\mysymbol* http://msdl.microsoft.com/download/symbols`
方法二：
新建一个环境变量`_NT_SYMBOL_PATH 值为` 设置值为 `cache*c:\mysymbol;srv*http://msdl.microsoft.com/download/symbols`

第一个只能缓存符号服务器形式的符号文件
第二种可以缓存远程共享形式的符号文件。

重启电脑，查看效果
如图：
![63695-5j5jbmhshsf.png](https://imgs.gnux.cn/usr/uploads/2019/04/2502615825.png)

![71985-c3ft6s6drsv.png](https://imgs.gnux.cn/usr/uploads/2019/04/990077428.png)