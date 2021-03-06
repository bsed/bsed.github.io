---
title: "使用DNS过滤屏蔽广告"
categories: [ "Linux" ]
tags: [ "linux","dns" ]
draft: false
slug: "use-dns-to-filter-screen-advertising"
date: "2016-09-26 20:41:00"
---

> 浏览器上的adBlock扩展非常好用，但是它只能屏蔽浏览器上的广告，像手机或应用中的广告它就无能为力了。本文介绍怎么使用DNS服务器过滤广告。

## 使用DNS级别屏蔽广告的优势

 - 支持手机应用内广告屏蔽
 - 支持平板

## 基本原理


<!--more-->


首先架设一台DNS服务器，做好广告服务商域名的过滤。当所查询的域名是列入黑名单中的广告服务器时，DNS服务器返回本地IP地址（还充当Web Server）。为了防止去掉广告后，网页布局发生改变，本地Web Server返回原广告大小的透明的假广告。

## 使用别人搭建好的DNS服务器

如果你不想搭建自己的DNS服务器，可以使用下面的DNS服务器：

    域名服务器(DNS) 地址：198.101.242.72
    备用DNS地址 (可选) ：23.253.163.53

## 进入路由器设置DNS（局域网内所有设备生效）：
![dns_01.png][1]

## DNS服务器：

![dns_02.png][2]
连接路由器的设备可能需要重新连接网络，才能使设置生效：

![dns_03.png][3]

也可以单独设置某个设备（单机生效）：

![dns_04.png][4]


  [1]: https://imgs.gnux.cn/usr/uploads/2016/09/2483664895.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/09/2566043060.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/09/1395249487.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/09/3993100424.png