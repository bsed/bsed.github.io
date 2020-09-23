---
title: git for windows 安装方法 图文详解
date: 2010-10-17 09:52:00
updated: 2016-01-24 08:44:15
tags: 
- memcached
categories: 
- php

---
简单来说Git是一个免费的、开源的版本控制软件，从功能上讲，跟我们比较熟悉的Subversion(SVN)这类版本控制软件没什么两样。由于工作的需求，需要在WinXP下安装git配合团队完成相应的工作，以前一直在别的系统下使用，今天经[Drupal花园](http://drupalchina.cn/)的[龙马](http://longma.org/)指点终于安装成功了，特意贴出来与有需要的童鞋一起分享。
获取Git软件：
大家可以通过两种方式获取Git的软件，一个是在[官网](http://git-scm.com/)上获取，另一个可以点击这[里](http://code.google.com/p/msysgit/)下载，当然了也可以从其他地方下载。只是有一点需要说明就是需要下载支持WinXp系统的版本。

Git在WinXP安装过程
在WinXP下安装Git详细步骤参考如下安装示意图：
![yigrherb_git-st1.png][1]
![yigrherb_git-st2.png][2]
![yigrherb_git-st3.png][3]
![yigrherb_git-st4.png][4]
![yigrherb_git-st5.png][5]
![yigrherb_git-st6.png][6]
![yigrherb_git-st7.png][7]
![yigrherb_git-st8.png][8]
![yigrherb_git-st9.png][9]
<!--more-->



点击上图的“Finish”后Git的安装就完成了，安装完成后需要在进行一下环境变量的设置，改变环境变量的具体步骤可以参考下图：

![yigrherb_git-st10.png][10]
![yigrherb_git-st11.png][11]
![yigrherb_git-st12.png][12]
![yigrherb_git-st13.png][13]
![yigrherb_git-st14.png][14]
![yigrherb_git-st15.png][15]
![yigrherb_git-st16.png][16]
![yigrherb_git-st17.png][17]
你使用文本编辑器打开“id_rsa.pub”就可以看到公钥的内容：

    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAs1c3zL3IbjhMbSCwooZ7WHlkga7xq+5inQUCTqmZ7xqBclvEwofDd4BYts99X+xfNXgGSlil85wrOfXX9ZH27ZmPR3Z8KWp3pekFOvk7iLmt0b9lZqhgpUll76Q7D2KED8se9gV4FNxg05ZK6AUZm70cBj9sZqjV//weE7ufkibcqd3ngHsTKB+0Js8NcMwldHmxIxZEwufxgT/D4EGQCA6ijjuj9ILIydzzLwWzKoH1wuGDU5LAE1qptIpAt50uWxPNlsBPoqZnbJjHg+As9If6tRkG4YzkwzSBTP725bJDcWkArhWRRkJ2cwUgcJYRyCY+8jI2oxDawYjWQ+w9HQ== airenliao@gmail.com

此时只要把你的公钥放进你项目的服务中就可以进行git的相关操作。
关于git的命令更多操作，可以到官网查阅，或点击这里查看更多的命令操作。
顺便列出解决中文目录支持问题：
1、ls不能显示中文目录
解决办法：在git/etc/git-completion.bash中增加一行：
  `alias ls='ls --show-control-chars --color=auto'`
2、git commit不能提交中文注释
解决办法：修改git/etc/inputrc中对应的行：
  `set output-meta on`
  `set convert-meta off `
3、git log无法显示中文注释
解决办法：在git/etc/profile中增加一行：
  `export LESSCHARSET=iso8859`
到这时有关于git在winxp下安装就介绍完了，希望这个能给一些朋友带来一定的帮助。同时更希望朋友们关注W3CPLUS，因为只有你们的关注才能带来我的成长。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/4181201657.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/01/1848762719.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/01/3579626036.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/01/4148581644.png
  [5]: https://imgs.gnux.cn/usr/uploads/2016/01/3173988625.png
  [6]: https://imgs.gnux.cn/usr/uploads/2016/01/2640760068.png
  [7]: https://imgs.gnux.cn/usr/uploads/2016/01/2879244017.png
  [8]: https://imgs.gnux.cn/usr/uploads/2016/01/3636628829.png
  [9]: https://imgs.gnux.cn/usr/uploads/2016/01/1600518138.png
  [10]: https://imgs.gnux.cn/usr/uploads/2016/01/3922978299.png
  [11]: https://imgs.gnux.cn/usr/uploads/2016/01/334172960.png
  [12]: https://imgs.gnux.cn/usr/uploads/2016/01/3657058440.png
  [13]: https://imgs.gnux.cn/usr/uploads/2016/01/2798733599.png
  [14]: https://imgs.gnux.cn/usr/uploads/2016/01/3205621119.png
  [15]: https://imgs.gnux.cn/usr/uploads/2016/01/1936474580.png
  [16]: https://imgs.gnux.cn/usr/uploads/2016/01/2280602189.png
  [17]: https://imgs.gnux.cn/usr/uploads/2016/01/4139531984.png