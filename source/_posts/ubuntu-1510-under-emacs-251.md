---
title: Ubuntu 15.10 下安装Emacs 25
date: 2015-11-14 08:54:00
updated: 2016-01-24 08:11:25
tags: 
- emacs
- helm
categories: 
- linux

---
## 先删除以前的 `emacs` 版本

可以进入 Ubuntu Software Center 里进行删除. 如果熟悉命令行, 也可以使用

`sudo apt-get remove emacs` 来进行删除.

## 安装核心依赖


    sudo apt-get install build-essential
    
    sudo apt-get build-dep emacs24

## 下载 Emacs 25.0.50.1 源码

    cd ~/Downloads && git clone git://git.savannah.gnu.org/emacs.git


<!--more-->


## 解压并进入该目录


    cd ~/Downloads && cd emacs

## 检测并安装

     #./configure --without-x
     ./configure

如果 configure 这一步出错, 一般是缺少其他相对应的依赖的, 这时, 可以使用 `aptitude` 这个工具来解决依赖的问题.

    make -j 4
    
    sudo make install

**注释**： `make -j 4` 可以提高编译速度,详情见[加速Linux程序编译](http://www.freemindworld.com/blog/2010/100105_make_complie_process_faster.shtml)
## 添加为 Unity 启动图标

    [Desktop Entry]
    Version=1.0
    Name=Emacs-25.1
    Exec=env UBUNTU_MENUPROXY=0 /usr/local/bin/emacs
    Terminal=false
    Icon=emacs
    Type=Application
    Categories=IDE
    X-Ayatana-Desktop-Shortcuts=NewWindow
    [NewWindow Shortcut Group]
    Name=New Window
    TargetEnvironment=Unity

## In end of data:
progmodes/cc-mode.el:1788:1:Warning: the following functions might not be
    defined at runtime: cc-bytecomp-load, cc-bytecomp-is-compiling,
    cc-bytecomp-restore-environment, cc-bytecomp-setup-environment,
    c-populate-syntax-table

    $ which emacs

/usr/local/bin/emacs

    $ /usr/local/bin/emacs --version

    GNU Emacs 25.0.50.1
    Copyright (C) 2015 Free Software Foundation, Inc.
    GNU Emacs comes with ABSOLUTELY NO WARRANTY.
    You may redistribute copies of Emacs
    under the terms of the GNU General Public License.
    For more information about these matters, see the file named COPYING.

**问题**

    Installing utilities for users to run.
    umask 022; /bin/mkdir -p "/usr/local/bin"
    for file in etags ctags emacsclient  ebrowse ; do \
      /usr/bin/install -c  ${file} "/usr/local/bin"/`echo ${file} | sed -e 's/$//' -e 's,x,x,'` ; \
    done
    for file in grep-changelog ; do \
      /usr/bin/install -c ./${file} "/usr/local/bin"/`echo ${file} | sed 's,x,x,'`  ; \
    done
    make[1]: Leaving directory '/home/kelvin/Downloads/emacs-25.0.50.1/lib-src'
    if test "no" = "no"; then \
      /usr/bin/install -c  src/emacs "/usr/local/bin/`echo emacs-25.0.50.1 | sed 's,x,x,'`" || exit 1 ; \
      chmod 1755 "/usr/local/bin/`echo emacs-24.5 | sed 's,x,x,'`" || true; \
      if test "x" = x; then \
        rm -f "/usr/local/bin/`echo emacs | sed 's,x,x,'`" ; \
        cd "/usr/local/bin" && ln -s `echo emacs-25.0.50.1 | sed 's,x,x,'` `echo emacs | sed 's,x,x,'`; \
      fi; \
    else \
      subdir=/site-lisp; \
      if [ -f "${subdir}/subdirs.el" ]; then true; else umask 022; /bin/mkdir -p "${subdir}"; (echo "(if (fboundp 'normal-top-level-add-subdirs-to-load-path)"; echo "    (normal-top-level-add-subdirs-to-load-path))") > "${subdir}/subdirs.el"; fi || exit 1; \
      rm -rf /share; \
    fi
    cd lib-src && make maybe-blessmail  \
      MAKE='make' archlibdir='/usr/local/libexec/emacs/25.0.50.1/x86_64-unknown-linux-gnu'
    make[1]: Entering directory '/home/kelvin/Downloads/emacs-24.5/lib-src'
    Assuming /var/mail is really the mail spool directory, you should
    run  lib-src/blessmail /usr/local/libexec/emacs/25.0.50.1/x86_64-unknown-linux-gnu/movemail
    as root, to give  movemail  appropriate permissions.
    Do that after running  make install.
    make[1]: Leaving directory '/home/kelvin/Downloads/emacs-25.0.50.1/lib-src'

> 下面部分方法不可用：

解决办法如下：

    make distclean
    
    ./configure --without-gsettings 
    
    make &&  make install

安装成功后出现如下提示：

    make[1]: Leaving directory `/root/Downloads/emacs-25.0.50.1/leim'
    cd lib-src; make maybe-blessmail  \
          MAKE='make' archlibdir='/usr/local/libexec/emacs/25.0.50.1/i686-pc-linux-gnu'
    make[1]: Entering directory `/root/Downloads/emacs-25.0.50.1/lib-src'
    make[1]: Nothing to be done for `maybe-blessmail'.
    make[1]: Leaving directory `/root/Downloads/emacs-25.0.50.1/lib-src'

成功后如图：
![yigrherb_emacs25.png][1]
在终端中输入emacs，即可打开软件
其他安装方法：
[http://emacs.stackexchange.com/questions/12772/prebuilt-ubuntu-emacs-25-1](http://emacs.stackexchange.com/questions/12772/prebuilt-ubuntu-emacs-25-1)

[http://dailyvine.gdp.xyz/archives/vinelinux1139](http://dailyvine.gdp.xyz/archives/vinelinux1139)


  [1]: https://imgs.gnux.cn/usr/uploads/2016/01/1515405449.png