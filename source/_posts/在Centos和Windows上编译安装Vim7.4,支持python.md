---
title: "在Centos和Windows上编译安装Vim7.4,支持python"
categories: [ "Vim" ]
tags: [ "windows","vim","centos6" ]
draft: false
slug: "centos6-5-compiler-vim74-suport-python27"
date: "2012-09-28 22:02:00"
---

我的Linux环境基于CentOS 6.5 64bit.
编译安装Vim7.4,支持python，我的Linux环境基于CentOS 6.5 64bit.


<!--more-->

#  Linux: #
1.卸载旧版本
  

      # rpm -qa|grep vim
        #yum remove vim vim-minimal vim-common vim-enhanced

2.安装依赖库
`yum install ncurses-devel python-devel`
3.解压，这个没话说。
  

      tar xvf vim-7.4.tar.bz2
        cd vim74

4.配置安装,根据需要自选配置，可以通过./configure –help查看特性列表

    ./configure --prefix=/opt/vim74 --with-features=huge --enable-cscope --disable-selinux --enable-xim --enable-fontset  --enable-pythoninterp --with-python-config-dir=/usr/lib64/python2.7/config
    make
    make install

5. 最后链接

    ln -s /opt/vim74/bin/vim /usr/bin/vim

 
#  Windows:  # 
1.下载Python [http://python.org/getit/](http://python.org/getit/ ) 

Py3K开始流行了，最好Py27和Py32都安装上

2.下载Mercurial  [http://mercurial.selenic.com/downloads/](http://mercurial.selenic.com/downloads/)版本控制软件，用来下载最新VIM源代码

3.下载VIM源代码

    E:\> hg clone https://vim.googlecode.com/hg/ vim

4.编译VIM

4.1 MinGW

    D:\vim\src>make.exe -f Make_ming.mak PYTHON="C:/Python27" PYTHON_VER=27 DYNAMIC_PYTHON=yes PYTHON3=C:\Python32 PYTHON3_VER=32 DYNAMIC_PYTHON3=yes USERNAME=nickname USERDOMAIN=mdil.org

    D:\vim\src>make.exe -f Make_ming.mak PYTHON="C:/Python27" PYTHON_VER=27 DYNAMIC_PYTHON=yes PYTHON3=C:\Python32 PYTHON3_VER=32 DYNAMIC_PYTHON3=yes USERNAME=nickname USERDOMAIN=mdil.org GUI=yes IME=yes

4.2 Visual C++

    D:\vim\src> nmake.exe -f Make_mvc.mak PYTHON="C:/Python27" PYTHON_VER=27 DYNAMIC_PYTHON=yes PYTHON3="C:/Python32" PYTHON3_VER=32 DYNAMIC_PYTHON3=yes USERNAME=nickname USERDOMAIN=mdil.org

    D:\vim\src> nmake.exe -f Make_mvc.mak PYTHON=C:\Python27 PYTHON_VER=27 DYNAMIC_PYTHON=yes PYTHON3=C:\Python32 PYTHON3_VER=32 DYNAMIC_PYTHON3=yes USERNAME=nickname USERDOMAIN=mdil.org GUI=yes IME=yes

5.测试

    " 测试VIM的Python支持
        function! s:py_ver()
        python << EOF
        import sys
        print(sys.version)
        EOF
        endfunction  
         
        function! s:py3_ver()
        python3 << EOF
        import sys
        print(sys.version)
        EOF
        endfunction
         
        command! -nargs=0 -bar PyV call s:py_ver()
        command! -nargs=0 -bar Py3V call s:py3_ver()

直接加载 `:so %`

然后试试命令 :PyV 和 :Py3V 能不能正确显示Python版本
这个文件可以直接保存为VIM的插件用，仅仅只是示例如何用Python写VIM插件 `:w $VIM\vimfiles\plugin\pyv.vim`
6.打包

    E:\vim\src\> xcopy ..\runtime ..\vim73 /e /q
    E:\vim\src\> xcopy *.exe ..\vim73
    E:\vim\src\> xcopy xxd\xxd.exe ..\vim73
    E:\vim\src\> xcopy GvimExt\gvimext.dll ..\vim73
    E:\vim\src\> cd \
    E:\> 7z a -t7z vim.7z vim\vim73
    E:\> 7z a -ttar vim.tar vim\vim73 -so | 7z a -txz vim.tar.xz -si