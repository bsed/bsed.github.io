---
title: wget 递归下载整个网站
date: 2015-11-19 09:50:00
updated: 2017-02-19 09:53:08
tags: 
- golang
- regex
categories: 
- go

---
有时间看到别人网站的页面比较漂亮，就想给扒皮下来，学习学习。分享一个我常用网站扒皮命令`wget`
这个命令可以以递归的方式下载整站，并可以将下载的页面中的链接转换为本地链接。
wget加上参数之后，即可成为相当强大的下载工具。
## wget命令详解
```
wget -r -p -np -k http://xxx.com/xxx
```


<!--more-->


参数详解：

 - `-r`, --recursive（递归） specify recursive download.（指定递归下载）
 - `-k`, --convert-links（转换链接） make links in downloaded HTML point to local files.（将下载的HTML页面中的链接转换为相对链接即本地链接）
 - `-p`, --page-requisites（页面必需元素） get all images, etc. needed to display HTML page.（下载所有的图片等页面显示所需的内容）
 - `-np`, --no-parent（不追溯至父级） don't ascend to the parent directory.
 - 另外断点续传用`-nc`参数 日志 用`-o`参数

拿我自己的网站扒皮试一下吧

执行 `wget -r -p -np -k https://tygasoft.com/` 命令

等网站递归下载完毕,你会发现你当前目录会有一个 `kelvin.mbioq.com`的目录

熟练掌握wget命令，可以帮助你扒皮网站。