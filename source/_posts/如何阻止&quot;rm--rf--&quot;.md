---
title: "如何阻止&quot;rm -rf /&quot;"
categories: [ "Css" ]
tags: [ "css","自动适配" ]
draft: false
slug: "linux-cmd-rm-rf"
date: "2015-01-03 12:43:00"
---

前天 敲命令的时候 敲的玩我 一部小心敲了 sudo rm -rf / 并且按了回车 过了几秒才反映过来 不过已经玩了 mac上一些运用已经被删除了 没做备份 只好花了一天时间重新安装。

那么问题来了，如何禁止执行 上面的命令 或者给个提示也行

[mcfog](http://segmentfault.com/u/mcfog)
1、 不要用rm，mac的话ac的话

    brew install rmtrash

然后用rmtrash替代rm（移动到废纸篓）

少数必须要用rm的情况，再三检查你的命令

2、 不要用sudo或root用户，必须要用的时候，再三检查你的命令

3、 sudo而且rm的时候，打完命令，检查完以后，去上个厕所回来再检查一遍再执行

4、 千万不要用alias或任何手段来改变rm命令的行为，千万不要用alias或任何手段来改变rm命令的行为。这很重要，因为用命令行就是为了习惯和了解服务器维护的知识经验，服务器上是不会有同样的alias的。养成慎用rm 慎用sudo 检查命令的好习惯才是正道。否则剧本就是你先`rm rf /`了自己，苦恼了半小时找了个alias或别的脚本之类的，几个月以后，登到生产环境欢乐地rm rf /了服务器，或者是`rm rf /usr /local/xxx`
日常使用的话参考前面，用rmtrash之类的替代命令就好
