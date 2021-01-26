---
title: "nrm: 快速切换 NPM 源 [转]"
categories: [ "Css" ]
tags: [ "segmentfault","nrm","npm" ]
draft: false
slug: "nrm-fast-switching-npm-source"
date: "2014-05-08 10:51:00"
---

以前我们介绍过cnpmjs.org和最近推出的淘宝 npm 两个 NPM 镜像。除此之外，还有一些国外的 NPM 镜像。不同地区访问不同的镜像速度可能有差异，然后各个镜像各自都可能有少数包暂时没有同步，因此，有时候需要切换 NPM 镜像。相比每次切换时都手动指定相应参数，使用[nrm](https://github.com/Pana/nrm) 要方便的多。

nrm 是一个 NPM 源管理器，允许你快速地在如下 NPM 源间切换：

    npm
    cnpm
    strongloop
    european
    australia
    nodejitsu
    taobao


<!--more-->


### 安装

    npm install -g nrm

### 使用
列出可选的源
  nrm ls                                                                                                    
    * npm ---- https://registry.npmjs.org/
      cnpm --- http://r.cnpmjs.org/
      taobao - http://registry.npm.taobao.org/
      eu ----- http://registry.npmjs.eu/
      au ----- http://registry.npmjs.org.au/
      sl ----- http://npm.strongloop.com/
      nj ----- https://registry.nodejitsu.com/

带 * 的是当前使用的源，上面的输出表明当前源是官方源。
切换

## 切换到taobao

`nrm use taobao`                                                                                                                          
    
       Registry has been set to: http://registry.npm.taobao.org/

### 增加源

你可以增加定制的源，特别适用于添加企业内部的私有源。私有源可以使用cnpmjs架设。

    nrm add  <registry> <url> [home]

删除源

    nrm del <registry>

测试速度

你还可以通过 `nrm test` 测试相应源的响应时间。

例如，测试官方源的响应时间：

    nrm test npm                                                                                                                               
    
      npm ---- 1328ms

测试所有源的响应时间：

    nrm test                                                                                                                                   
    
      npm ---- 891ms
      cnpm --- 1213ms
    * taobao - 460ms
      eu ----- 3859ms
      au ----- 1073ms
      sl ----- 4150ms
      nj ----- 8008ms

**注意**，为了取得较准确的结果，可以考虑多次测试取平均值。
许可

nrm 为开源软件，使用 MIT 许可。
### 项目主页

[github.com/Pana/nrm](http://github.com/Pana/nrm)

原文：[http://segmentfault.com/a/1190000000473869](http://segmentfault.com/a/1190000000473869)