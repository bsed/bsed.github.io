---
title: fcitx安装以及在emacs中输入中文
date: 2014-10-11 09:27:00
updated: 2016-10-03 09:34:47
tags: 
- meta
- mobile
- html
categories: 
- css

---
如何安装 GNU Emacs 25.1 在Ubuntu 16.04. Linux Ubuntu 的 GNU Emacs 文本编辑器是最受欢迎的 Emacs 文本编辑器. Emacs 是最古老和最多才多艺的文本编辑器可用于 Linux 和基于 UNIX 的系统之一. 它已经存在很长时间 (二十余年为 GNU emacs 的) 和是很出名的是其功能强大且丰富的编辑功能. 它是由 GNU 项目创始人理查德 · 斯托曼创建.

## GNU EMACS 25.1

终于到达了 GNU Emacs 文本编辑器 25.1 释放昨晚. 这里是如何在 ubuntu 系统中安装它 16.04 和 Ubuntu 16.10.

**Emacs的 25.1 发布亮点:**

 - Emacs 现在可以加载共享/动态库 (模块)
 - 开罗绘图的实验支持
 - 增强的网络的安全 (TLS/SSL 证书的有效性和类似)
 - 新小模式电-报价-模式使用弯的引号作为您键入
 - 在 isearch.el 折叠支持的字符
 - Xwidgets: 一项新功能嵌入本机小部件里面 Emacs 缓冲区
 - 新的和改进的设施，用于插入 Unicode 字符


<!--more-->


## 安装GNU EMACS的 25.1 在 UBUNTU 中

在你安装 GNU Emacs 之前 25.1 在Ubuntu, 安装依赖使用下面的命令:
```
sudo apt install build-essential checkinstall
sudo apt-get build-dep emacs24
```
一旦安装了依赖项, 下载并安装 GNU Emacs 25.1 在Ubuntu 16.04, Ubuntu的 15.04, Ubuntu的 14.04 和其它 Ubuntu 衍生品:
```bash
sudo apt-get update
sudo wget http://ftp.gnu.org/gnu/emacs/emacs-25.1.tar.gz
sudo tar xzvf emacs-25.1.tar.gz
cd ~/Downloads/emacs-25.1
./configure
make
sudo checkinstall
```
一旦安装, Emacs 从 Ubuntu 破折号或打开运行命令 `emacs` 在终端来启动应用程序.

## 删除 EMACS

如果您想要卸载并删除 Emacs 从 Ubuntu 系统, 运行以下命令:
```bash
sudo dpkg -r emacs-25
```

## fcitx安装以及在emacs中输入中文

安装步骤如下：

1：如果系统中有scim输入法的话，先进行卸载了，不然可能会和后面的fcitx的使用有冲突，卸载命令：
```bash
sudo apt-get remove scim
```
2：安装fcitx（安装前更新下源最好了），安装命令：

```bash
sudo apt-get update

sudo apt-get install fcitx
```
3：配置输入法：

1：在 `etc/X11/Xsession.d/` 目录下，建立“`95fcitx_start`”文件，然后对其编辑，“`sudo emacs /etc/X11/Xsession.d/95fcitx_start`”，内容输入：
```bash
export LC_CTYPE=zh_CN.UTF-8

export XMODIFIERS=@im=fcitx

export XIM=fcitx

export XIM_PROGRAM=fcitx

fcitx
```
6.保存退出后，给该文件添加可执行权限，输入“sudo chmod +x /etc/X11/Xsession.d/95fcitx_start”

然后改下环境变量：sudo gedit~/.bashrc,在其中添加以下变量：
```
#fcitx
export XMODIFIERS="@im=fcitx"
export QT_IM MODULE=xim
export GTK_IM_MODULE=xim
```
重启电脑，小企鹅就会开机自动启动了，通过CTRL+SPACE就可以使用小企鹅中文输入法。

要在emacs中输入中文，直接按照下面链接就OK了，没必要在这再写了，尊重原创：

http://blog.csdn.net/pcliuguangtao/article/details/6264832