---
title: Emacs包管理系统ELPA配置
date: 2015-07-13 20:09:00
updated: 2015-08-13 20:14:01
tags: 
- golang
- slice
- array
categories: 
- go

---
话说emacs24更新的最大亮点就是内建ELPA支持,之前的版本是需要自己下载安装的.
 
那么进入正题,ELPA是一个emacs的插件包管理系统,可以帮你下载相应的插件，
并添加相应的load-path等。个人觉得应该是emacs下需要地一个配置的插件，
如果你想好好组织你的emacs配置的话。配置过程并不难。
 
 
### 配置
elpa好像需要cl(内建支持)，所以先require一下。
之后记得要设置下载源（package-archives).
下面两个配置一个是用来更新源列表的（个人理解),一个是用来自动安装插件的（就是如果你本地
没有安装的话，就会自动安装，并下载本地.emacs.d/elpa目录下，我这里配置了
yasnippet/auto-complete/ecb/google-translate，你可以自己在添加
最后一个是设置一些变量的，暂时这么看这吧。


<!--more-->


    #+BEGIN_SRC elisp
    ;;;; package system,ELPA
    (require 'cl)
    (require 'package)
    (add-to-list 'package-archives '("marmalade" . "http://marmalade-repo.org/packages/") t)
    (package-initialize)
     
    ;; auto installed package
    (when (not package-archive-contents)
      (package-refresh-contents))
     
    ;; setting default package to be installed
    (defvar my-default-packages '(ecb
                                  yasnippet
         auto-complete
                                  google-translate))
    (dolist (p my-default-packages)
      (when (not (package-installed-p p))
        (package-install p)))
     
    ;; normal useful variable set
    ;; normal useful variable set
    ;;(setq dotfiles-dir "~/.emacs.d");;这个我是在.emacs.d/init.el里定义的
    (setq autoload-file (concat dotfiles-dir "/loaddefs.el"))
    (setq package-user-dir (concat dotfiles-dir "/elpa"))
    (setq custom-file (concat dotfiles-dir "/custom.el"))
     
    (provide 'xxx)
    #+END_SRC

上面陪好后,在主引导文件中 require一下就哦看了
 
* 在讲讲基本的使用
** 安装插件
m-x package-install
** 列表
m-x package-list-packages
会打开个buffer.其中有写特殊按键绑定。如<CR>，p,n等，具体内容自己C-h m查看吧