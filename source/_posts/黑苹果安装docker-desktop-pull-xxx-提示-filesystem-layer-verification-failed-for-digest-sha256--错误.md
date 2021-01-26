---
title: "黑苹果安装docker desktop pull xxx 提示 filesystem layer verification failed for digest sha256: 错误"
categories: [ "系统" ]
tags: [ "mac","docker" ]
draft: false
slug: "black-apple-installed-docker-desktop-pull-xxx-prompt-filesystem-la-x-yer-verification-failed-for-digest-sha256-error"
date: "2020-07-30 12:37:00"
---

黑苹果15.6 安装docker desktop pull xxx 提示如下错误。
filesystem layer verification failed for digest sha256:717377b83d5cc3982e90a975a487fd062e6f0185347b756a360dedafd4a3f915
![iShot2020-07-30下午12.29.39.png][1]


<!--more-->

群友【小白~i7/sn750/2cs(ax200  10:41:52】贴图：

![QQ20200730-1.jpg][2]
上面是异常
下面是正常
![QQ20200730-2.png][3]


docker 破案的答案

https://github.com/search?q=org%3Aacidanthera+docker&type=Code
https://github.com/acidanthera/bugtracker/issues/1035

还真是仿冒CPUID的锅
![cpuid_docker.png][4]
![iShot2020-07-30下午06.05.59.png][5]
上传时间应该是29号吧，由宪武大佬上传。
球球群号：946132482


  [1]: https://imgs.gnux.cn/usr/uploads/2020/07/2283238994.png
  [2]: https://imgs.gnux.cn/usr/uploads/2020/08/1729562569.jpg
  [3]: https://imgs.gnux.cn/usr/uploads/2020/07/3400525703.png
  [4]: https://imgs.gnux.cn/usr/uploads/2020/07/3573323014.png
  [5]: https://imgs.gnux.cn/usr/uploads/2020/07/533467158.png