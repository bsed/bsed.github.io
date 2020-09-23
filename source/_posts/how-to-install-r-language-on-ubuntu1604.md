---
title: 如何在ubuntu16.04上安装 R语言
date: 2016-11-27 20:30:00
updated: 2016-11-27 22:15:05
tags: 
- proxy
- shadowsocks5
categories: 
- linux

---
>  从明天开始就要学些[谢佳标](https://ask.hellobi.com/people/xiejiabiao)老师的R语言培训了。晚上就学习下如何在Ubuntu 16.04 上搭建 R语言开发环境。

## 安装R-Base

你可以在Dash 面板下的 Software Center 上安装**R-Base**，这是很容易的。然而，Software Center上面的版本比较陈旧，而好多插件 packages 是基于 R-Base 的新版本开发的，所以，最简单的方法是从 Cran 服务器上安装 R-Base，下面就详细介绍下安装步骤。


<!--more-->


### 增加一个 R 仓库

首先，我们需要添加一行 到 `/etc/apt/sources.list` ,注意我的Ubuntu 版本是  `xenial`  即 `16.04`,假如你的版本不同的话，你可以改变 'xenial' 为 你系统版本所对应的名字。

ubuntu16.04

```bash
sudo echo "deb http://cran.rstudio.com/bin/linux/ubuntu xenial/" | sudo tee -a /etc/apt/sources.list
```

如果是 ubuntu 16.10 的话.

```bash
sudo echo "deb http://cran.rstudio.com/bin/linux/ubuntu yakkety/"  |  sudo tee -a /etc/apt/sources.list
```
## 添加 R 到 Ubuntu keyring上
```bash
 gpg --keyserver keyserver.ubuntu.com --recv-key 51716619E084DAB9
```
将会出现下面的提示：
```bash
gpg: key 51716619E084DAB9: public key "Michael Rutter <marutter@gmail.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```
如果 key 后面的数字不一样的话， 修改 `-recv-key`后面的数字 为 提示所显示的数字 `51716619E084DAB9`

接着执行：
```bash
gpg -a --export 51716619E084DAB9 | sudo apt-key add -
```
### 安装R-Base
```bash
sudo apt-get update
sudo apt-get install r-base r-base-dev
```

## 安装 R-Studio
从[**R-Studio 官网**]((https://www.rstudio.com/products/rstudio/download/)) 安装 所需要的版本 
笔者的系统是x64位的，所以选择下面的安装包

```bash
sudo apt-get install gdebi-core
wget https://download1.rstudio.org/rstudio-1.0.44-amd64.deb
sudo gdebi -n rstudio-1.0.44-amd64.deb
rm rstudio-1.0.44-amd64.deb
```
效果如下：
![ubuntu_rstudio_install.png][1]

## 安装 RGtk2 报错

Error in loadNamespace(name) : there is no package called ‘RGtk2’
But when I try to install RGtk2 I get this:

configure: error: GTK version 2.8.0 required
ERROR: configuration failed for package ‘RGtk2’

```bash
sudo apt-get install libgtk2.0-dev
sudo apt-get install libgtk2.0
```
或者：

通过  [http://rattle.togaware.com/rattle-install-troubleshooting.html](http://rattle.togaware.com/rattle-install-troubleshooting.html) 安装。

```bash
sudo apt-get install wajig
wajig install libgtk2.0-dev
```

## 设置镜像
```r
    local(
    {
    r <- getOption("repos")
    r["CRAN"] <- "http://mirrors.xmu.edu.cn/CRAN/"
    options(repos=r)
    })
    install.packages("stringr")
    library(arulesSequences)
```
备用镜像：

[http://mirror.bjtu.edu.cn/cran/](http://mirror.bjtu.edu.cn/cran/)

[http://mirrors.ustc.edu.cn/CRAN/](http://mirrors.ustc.edu.cn/CRAN/)

[http://mirrors.tuna.tsinghua.edu.cn/CRAN/](http://mirrors.tuna.tsinghua.edu.cn/CRAN/)

[http://mirrors.xmu.edu.cn/CRAN/](http://mirrors.xmu.edu.cn/CRAN/)




*参考：*

 - [http://stackoverflow.com/questions/28533667/error-on-installing-rattle-in-ubuntu](http://stackoverflow.com/questions/28533667/error-on-installing-rattle-in-ubuntu)
 - [http://blog.fens.me/r-install-ubuntu/](http://blog.fens.me/r-install-ubuntu/)

  [1]: https://imgs.gnux.cn/usr/uploads/2016/11/102157262.png