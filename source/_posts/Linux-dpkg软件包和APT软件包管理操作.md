---
title: "Linux-dpkg软件包和APT软件包管理操作"
categories: [ "Linux" ]
tags: [ "linux","ubuntu" ]
draft: false
slug: "linuxdpkg-software-package-and-apt-package-management-operation"
date: "2012-11-19 16:18:00"
---

## dpkg软件包管理工具：

dpkg(Debian Package)管理工具，Ubuntu 相关的软件包文件使用 .deb 后缀，就是因为Ubuntu 与 Debian GNU/Linux 发行版有着紧密的关系。如果我们手动下载了.deb 包，可以通过以下命令进行操作：
```
//安装 Debian 包裹文件：  
sudo dpkg -i debfilename  
  
//列出包裹包含的内容：  
sudo dpkg -c debfilename  
  
//从 .deb 文件中提取包裹信息：  
sudo dpkg -I debfilename1  
  


<!--more-->


//卸载一个已安装的包裹：  
sudo dpkg -r packagename  
  
//完全清除一个已安装的包裹。和 remove 不同的是，remove 只是删掉数据和可执行文件，purge 另外还删除所有的配制文件：  
sudo dpkg -P packagename  
  
//列出包裹安装的所有文件清单。（同时请看 dpkg -c 来检查一个 .deb 文件的内容）：  
sudo dpkg -L packagename  
  
//显示已安装包裹的信息：  
sudo dpkg -s  

// 从文件夹里安装软件
sudo dpkg -iR /opt/software

//显示已安装软件列表
dpkg -l

//查看软件安装目录
dpkg -L atom

//查看文件属于哪个软件
dpkg -S /usr/bin/atom

//重新配制一个已经安装的包裹，如果它使用的是 debconf (debconf 为包裹安装提供了一个统一的配制界面)，你能够重新配制 debconf 它本身：  
sudo dpkg-reconfigure packagename  
  
//获取软件包裹状态：  
sudo dpkg --get-selections | grep keyname  
  
//设置软件包裹状态，比如 HOLD 某个包裹不允许升级：  
echo "packagename hold" | sudo dpkg --set-selections  
sudo echo "mysql-server hold" | sudo dpkg --set-selections
  
//从 HOLD 状态恢复软件包裹标志位：  
echo "packagename install" | sudo dpkg --set-selections  

//查询当前系统内所有软件包的状态，命令为：
sudo dpkg --get-selections | more  

//查询当前系统被锁定不更新的软件包状态(hold)，命令为
sudo dpkg --get-selections | grep hold

//修正依赖关系损坏的包裹：  
sudo apt-get -f install  
```
## APT软件包管理：

APT (Advanced Packaging Tool高级软件包工具) 是一个强大的包管理系统，而那些图形化程序如添加/删除应用程序 都是建立在它的基础之上的。有了dpkg后,Debian再次开发了apt,它能自动处理依赖文件并维护已有的配置文件,快速,实用,高效。当我们安装某个程序时，如果没有，它会从服务器上查找并自动下载。
 
APT 使用的一些常用命令：
```
//搜索包   
apt-cache search package   
  
//获取包的相关信息，如说明、大小、版本等  
apt-cache show package   
  
//安装包   
sudo apt-get install package   
  
//列出更多命令和选项：   
apt-get help   
例如：  
sudo apt-get install eclipse  
sudo apt-get install sun-java6-jdk   
  
//下载安装程序到你的/var/cache/apt文件夹里面然后进行安装。   
sudo apt-get install package - - reinstall  
  
//修复安装"-f = ――fix-missing"   
sudo apt-get -f install   
  
//删除包   
sudo apt-get remove package   
  
//删除包，包括删除配置文件等   
sudo apt-get remove package - - purge   
  
//获取新的软件包列表  
sudo apt-get update   
  
//升级有可用更新的系统   
sudo apt-get upgrade   
  
//使用 dselect 升级   
sudo apt-get dselect-upgrade   
  
//升级系统   
sudo apt-get dist-upgrade   
  
//了解使用依赖   
apt-cache depends package   
  
//是查看该包被哪些包依赖   
apt-cache rdepends package   
  
//安装相关的编译环境   
sudo apt-get build-dep package   
  
//下载该包的源代码   
apt-get source package   
  
//检查是否有损坏的依赖  
sudo apt-get check    
```

## 将 .rpm 文件转为 .deb 文件：
对于软件包为.rpm后缀的 Red Hat 软件包管理器文件。我们并不建议在 Ubuntu 系统中安装它们。在绝大多数情况下，Ubuntu 自身的 .deb 软件包是可用的。然而，如果绝对必要，可以使用程序 alien 将 .rpm 文件转化为.deb 文件。安装 alien 程序：

在终端使用管理权限运行命令 `sudo alien package_file.rpm`

