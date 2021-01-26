---
title: " Windows下安装Vim插件管理Vundle"
categories: [ "Vim" ]
tags: [ "vundle" ]
draft: false
slug: "windows-vim-install-vundle"
date: "2014-12-23 10:11:00"
---

VIM是编辑器之神，这个就不用说了，越使用越会体会到VIM的强大与便利。但是它的强大建立在众多插件组合之上，而Vim本身缺乏对插件的有效管理，安装插件并配置_vimrc文件非常不便。gmarik受到Ruby的bunler的启发，开发了vundle这个vim插件，可以将插件分到不同的目录管理，更厉害的是可以很简单的添加需要安装的插件，然后通过指令简单的安装和更新，下面就介绍VIM和Vundle的安装。

1.安装Git。


<!--more-->


Vundle基于Git架构，每一个插件都是一个项目的Repository，通过Vundle可以用简单的指令，一键安装/更新/删除所有插件。

下载msysgit并安装（具体可见[http://drupalchina.cn/content/windows-xi-tong-xia-gitan-zhuang-tu-jie](http://drupalchina.cn/content/windows-xi-tong-xia-gitan-zhuang-tu-jie)），并将Git 的安装路径加入环境变量Path，如
`D:\Program Files\Git\cmd`
然后运行cmd，输入
`git --version`
如果能显示Git版本信息，说明安装成功。
2. 配置Curl脚本。

在Windows下还需要建立一个Curl脚本用于Vundle的远程链接。在Git的路径下新建一个空文本文件，改名为curl.cmd，编辑内容为

    @rem Do not use "echo off" to not affect any child calls.
    @setlocal
    
    @rem Get the abolute path to the parent directory, which is assumed to be the
    @rem Git installation root.
    @for /F "delims=" %%I in ("%~dp0..") do @set git_install_root=%%~fI
    @set PATH=%git_install_root%\bin;%git_install_root%\mingw\bin;%PATH%
    
    @if not exist "%HOME%" @set HOME=%HOMEDRIVE%%HOMEPATH%
    @if not exist "%HOME%" @set HOME=%USERPROFILE%
    
    @curl.exe %*

保存后运行cmd，输入
`curl --version`  
如果可以看到版本信息说明配置成功。
3. 安装Vundle。

Git安装完成后，从官网Clone Vundle到Vim的安装路径下，如
`git clone https://github.com/gmarik/vundle D:\Vim\vimfiles\bundle\vundle`
此时Vim的目录结构应该如下所示
D:Vim
```
+---vim73

+---vimfiles

      +---bundle

             +---vundle

                    +---autoload
```
4. 配置Vundle。

按照Vundle官方给出的配置，所有通过Vundle安装的插件会被安装到Windows的用户目录下，这里做了修改，直接安装到Vim目录下方便管理。

编辑_vimrc文件，加入

    filetype off
    " 此处规定Vundle的路径
    set rtp+=$VIM/vimfiles/bundle/vundle/
    call vundle#rc('$VIM/vimfiles/bundle/')
    Bundle 'gmarik/vundle'
    filetype plugin indent on
    
    " original repos on github<br>Bundle 'mattn/zencoding-vim'
    Bundle 'drmingdrmer/xptemplate'
     
    " vim-scripts repos
    Bundle 'L9'
    Bundle 'FuzzyFinder'
    Bundle 'bufexplorer.zip'
    Bundle 'taglist.vim'
    Bundle 'Mark'
    Bundle 'The-NERD-tree'
    Bundle 'matrix.vim'
    Bundle 'closetag.vim'
    Bundle 'The-NERD-Commenter'
    Bundle 'matchit.zip'
    Bundle 'AutoComplPop'
    Bundle 'jsbeautify'
    Bundle 'YankRing.vim'
     
    filetype plugin indent on     " required! 

5.安装插件
安装插件具体可看[http://yp.oss.org.cn/blog/show_resource.php?resource_id=1561](http://yp.oss.org.cn/blog/show_resource.php?resource_id=1561)。

后面的链接是我的VIM配置文件：https://github.com/cgnerds/vim。

参考链接：

1.AlloVince的VIM配置方案：[http://yp.oss.org.cn/blog/show_resource.php?resource_id=1561](http://yp.oss.org.cn/blog/show_resource.php?resource_id=1561)

2.使用Vundle来管理Vim插件：[http://www.cnblogs.com/qiangji/archive/2011/07/31/Vundle.html](http://www.cnblogs.com/qiangji/archive/2011/07/31/Vundle.html)

3.VIM之Vundle：[http://blog.csdn.net/codebistu/article/details/8257138](http://blog.csdn.net/codebistu/article/details/8257138)

4.值得参考的VIM配置文件

[https://github.com/asins/vim/blob/master/_vimrc](https://github.com/asins/vim/blob/master/_vimrc)

[https://github.com/AlloVince/vim-of-allovince/blob/master/_vimrc](https://github.com/AlloVince/vim-of-allovince/blob/master/_vimrc)