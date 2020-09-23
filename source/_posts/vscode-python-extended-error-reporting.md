---
title: the environment variable path seems to have some paths containing characters
date: 2018-11-14 13:27:00
updated: 2018-11-14 14:33:59
tags: 
- css
- csscomb
categories: 
- css

---
VS code 在运行py程序时报错了，错误如下：
![44563-rpgmogjleb.png](https://imgs.gnux.cn/usr/uploads/2018/11/1340241264.png)

> The environment variable ‘Path’ seems to have some paths containing
> characters (‘;’, ‘“‘ or ‘;;’). The existence of such characters are
> known to have caused the Python extension to not load. If the
> extension fails to load please modify your paths to remove these
> characters.

说环境变量有问题，如果你是 Windows10 的话，如图：


<!--more-->


![20181114142349.png][1]

然而根本找不到所谓的';', '"' or ';;'

应该点击编辑文本，默认的那个编辑视图是不显示';' 或者'"'的，复制出来搜索一下看有没有';;'啥的，用户环境变量没有就去看看系统环境变量

![86002-ldybls42xnf.png](https://imgs.gnux.cn/usr/uploads/2018/11/1650834532.png)

copy 后粘贴到 notepad 上 搜一下就能发现问题了。如图，笔者多了个‘"’。 ok, 问题解决了。

![60007-hhaqku6rpnp.png](https://imgs.gnux.cn/usr/uploads/2018/11/245689128.png)

  [1]: https://imgs.gnux.cn/usr/uploads/2018/11/2403208876.png