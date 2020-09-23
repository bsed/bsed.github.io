---
title: [解决]java jdk11，发现安装路径没有jre文件夹
date: 2019-02-24 08:11:00
updated: 2019-02-24 20:14:06
tags: 
- spam
categories: 
- default

---
安装jre，需要到jdk目录下面去
![02306-eenezsmwmjc.png](https://imgs.gnux.cn/usr/uploads/2019/02/1133029302.png)
在此处打开命令窗口，执行如下命令：


<!--more-->


![52006-bww8u0li4s.png](https://imgs.gnux.cn/usr/uploads/2019/02/2759132650.png)
```
bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre
```
执行完之后就会生成jre啦