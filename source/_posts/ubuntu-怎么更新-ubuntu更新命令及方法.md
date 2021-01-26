---
title: "ubuntu 怎么更新?ubuntu更新命令及方法"
categories: [ "Java" ]
tags: [  ]
draft: false
slug: "how-to-update-the-ubuntu-ubuntu-update-command-and-method"
date: "2015-07-05 07:27:00"
---

安装Ubuntu系统后，第一件事就是更新系统源。由于系统安装的默认源地址在英国，作为Ubuntu的主源，国内连接速度非常慢，所以我们要将它换成就近的快速源。

网上有些人说CN99是国内最快的源，但不同的网络状况连接以下源的速度不同, 所以cn99并不一定是最快的源, 建议在添加前手动验证一下源的连接速度(ping下就行),比如说北京网通ADSL用户连接cn99就非常慢,而ftp.sjtu.edu.cn则相对较快，选择最快的源可以节省大批下载时间。


<!--more-->


首先备份源列表，而后用gedit或其他编辑器打开源列表文件：

      sudo cp /etc/apt/sources.list /etc/apt/sources.list_backup

      sudo gedit /etc/apt/sources.list

从下面列表中选择合适的源，替换掉文件中所有的内容，保存编辑好的文件然后执行源的更新（注意：不同的Ubuntu版本对应的源是不同的，千万不要弄错了）：

      sudo apt-get update

如果更新很慢，可以用上面的方法换一个源，然后再试，直到成功为止。然后，执行真正的系统更新：sudo apt-get upgrade；如下图所示：
湾源可能会慢一些，耐心等一下，换源后一般下载速度都有几十K，上百K也很正常。

ubuntu更新命令

       常用的APT命令参数：
　　apt-cache search package 搜索包
　　apt-cache show package 获取包的相关信息，如说明、大小、版本等
　　sudo apt-get install package 安装包
　　sudo apt-get install package - - reinstall 重新安装包
　　sudo apt-get -f install 修复安装"-f = ——fix-missing"
　　sudo apt-get remove package 删除包
　　sudo apt-get remove package - - purge 删除包，包括删除配置文件等
　　sudo apt-get update 更新源
　　sudo apt-get upgrade 更新已安装的包
　　sudo apt-get dist-upgrade 升级系统
　　sudo apt-get dselect-upgrade 使用 dselect 升级
　　apt-cache depends package 了解使用依赖
　　apt-cache rdepends package 是查看该包被哪些包依赖
　　sudo apt-get build-dep package 安装相关的编译环境
　　apt-get source package 下载该包的源代码
　　sudo apt-get clean && sudo apt-get autoclean 清理无用的包
　　sudo apt-get check 检查是否有损坏的依赖

