---
title: "在ubuntu16.04上编译vim8 支持 perl,ruby python2, python3 lua 等"
categories: [ "Linux","Vim" ]
tags: [ "ubuntu","vim","vim8" ]
draft: false
slug: "compiled-on-the-vim8-perl-support-ruby-python2-python3-lua-ubuntu1604-etc"
date: "2016-09-14 10:30:00"
---

> VIM是VI的改进，是一个免费的、开源的、高效的基于命令行的文本编辑器，vim 8发布于2016年9月12日。

## Vim 8的新功能：

- 异步I/O的支持，渠道，JSON
- 任务jobs
- 定时器Timers
- 分片，Lambda表达式和闭包的支持
- 包
- 新风格的测试
- 通过时间合并Viminfo 
- GTK+ 3的支持
- MS Windows DirectX的支持

## 如何在Linux上安装Vim 8


<!--more-->


> 安装Vim 8最简单的方法是通过GitHub库。首先在linux 系统上安装 git。

**Debian / Ubuntu / Linux Mint**

```bash
sudo apt install git
```

**Fedora**

```bash
sudo dnf install git
```

**Arch Linux/Manjaro/Apricity OS**

```bash
sudo pacman -S git
```

然后GitHub克隆VIM库

```bash
git clone https://github.com/vim/vim.git
cd vim
```

## 配置编译环境

```bash
./configure  --prefix=/usr/local --with-vim-name=vim8 --with-compiledby="argonauts12" --with-features=huge --enable-gui=auto --with-x --enable-rubyinterp --with-ruby-command=/usr/bin/ruby --enable-luainterp --with-lua-prefix=/usr/include/lua5.3  --enable-perlinterp --enable-pythoninterp  --with-python-config-dir=$(/usr/bin/python2.7-config --configdir) --enable-fontset --enable-cscope   --enable-gtk2-check --enable-gnome-check --enable-gui=auto   --enable-fail-if-missing --enable-multibyte --enable-python3interp --with-python3-config-dir=$(/usr/bin/python3.5-config --configdir) --with-compiledby="kelvin" 
```

*注意：*
> `$(shell python-config --configdir) `  shell 替换为自己的shell 例如 我的是 bash ，那就改为 `$(bash python-config --configdir) `

## 安装 ncurses lib

```bash
sudo apt install ncurses-dev
sudo apt install libperl-dev
sudo apt install ruby-dev
sudo apt install pyt-dev
sudo apt install liblua5.3-dev

sudo yum  install  ncurses-devel
sudo yum install libperl-dev
sudo yum install ruby-dev
sudo yum install pyt-dev
sudo yum install liblua5.3-dev
```

## 编译源代码

```bash
make
```

## 安装
```bash
sudo make install
```

安装完成后，可以使用以下命令检查 v**im版本**。

```bash
vim8 --version | head -n 1                                                                                            (master) (09/14 22:09:44
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Sep 14 2016 22:08:08)
```
如图：
![vim8.png][1]

*配置文件出错的话建议 看 下面的链接:*

[https://gist.github.com/jdewit/9818870](https://gist.github.com/jdewit/9818870)
[https://github.com/Shougo/neocomplete.vim/issues/31](https://github.com/Shougo/neocomplete.vim/issues/31)

或者你需要 移除 vim7*

由于之前配置vim出错，所以vim使用起来比较麻烦。
```bash 
sudo apt-get remove vim         //输入re按下tab直接显示remove
sudo apt-get remove  vim-runtime
sudo apt-get remove vim -tiny
sudo apt-get remove vim-common
 ```
纯净移除的话：

```bash
sudo apt-get purge --auto-remove vim
sudo apt-get purge --auto-remove vim-*
```
  [1]: https://imgs.gnux.cn/usr/uploads/2016/09/3810597579.png