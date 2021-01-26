---
title: " gitExtension 合并与衍合"
categories: [ "Linux" ]
tags: [ "windows","gitExtension" ]
draft: false
slug: "gite"
date: "2019-02-27 08:43:00"
---

注意到:以下的操作主要是用gitExtension来操作的。

gitExtension配置教程:http://www.cnblogs.com/sorex/archive/2011/08/10/2132359.html

内容提要:
```
Q1、git如何让develop分支合并到master

Q2、git如何让develop分支衍合到master

Q3、git如何搭建server。
```
-----------------------------------------------------------------------------------


<!--more-->


## 关于Q1：
```
step0：将develop分支pull最新版本。

step1:切换到master分支。

step2:在develop分支上右击，选择合并到develop。

step3:至此,合并完成。

step4：如果有冲突，用kdiff3,可以分别点击A,B,C，最终结果在最下方，改好后，点击保存即可。
```
-----------------------------------------------------------------------------------

## 关于Q2：
```
step0：将develop分支pull最新版本。

step1:切换到master分支。

step2:在develop分支上右击，选择衍合到develop。

step3:至此,衍合完成。

step4：如果有冲突，用kdiff3,可以分别点击A,B,C，最终结果在最下方，改好后，点击保存即可。
```
## 总结：

合并和衍合，结果是一样的

只是衍合后，时间轴看上去是在一起的，像是从头到尾一个人开发一样

合并后，时间轴不在一起的，

这块的资料，可以仔细看git官方资料，如下：

https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88


-----------------------------------------------------------------------------------

## 关于Q3：

1、分几种操作系统。

2、在ubuntu上。

    基本思路参考看廖雪峰的教程：http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000
    但是在创建客户端公钥这一块有点问题，按照他的意思应该是在linux上运行ssh-keygen -t rsa.然而我的git客户端是在windows上的，在哪运行这个命令呢？用putty生成的ssh的公钥死活不行。结果在gitExtension的官网上发现了端倪，可以用openssh。它会弹出一个DOS框让你输命令的。具体参考链接:http://git-extensions-documentation.readthedocs.org/en/latest/remote_feature.html

3、在centos5上。

    除了第一步不一样外，其他的一样。
    第一步教程1:http://blog.csdn.net/whu_zhangmin/article/details/11922609
    教程2：http://freeloda.blog.51cto.com/2033581/1410562


 -----------------------------------------------------------------------------------


## 注意点：

1、git在创建了多个分支后，git pull或者git push并不是指当前分支。需要单独指定：如git push origin master,git pull origin develop.

原文: [https://wandejun1012.iteye.com/blog/2230453](https://wandejun1012.iteye.com/blog/2230453)