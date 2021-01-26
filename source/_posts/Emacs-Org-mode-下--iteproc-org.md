---
title: "Emacs Org-mode 下  iteproc-org"
categories: [ "Vim" ]
tags: [ "emacs" ]
draft: false
slug: "emacs-orgmode"
date: "2019-06-12 22:13:00"
---

本文所有命令及快捷键基于Emacs Prelude配置文件。

1 安装插件
1.1 Org-ref插件的安装
org-ref是一个在org-mode中智能插入参考文献的包，可以用如下命令安装org-ref插件：

M-x package-install org-ref

1.2 Citeproc-org的安装
citeproc-org是一个用csl文件管理参考文献输入样式的包，目前在melpa源中没有这个包。因此，首先需要在git-hub上下载最新的[citeproc-org安装包](https://github.com/andras-simonyi/citeproc-org) ，再使用如下命令进行安装：


<!--more-->


`M-x package-install-file 安装压缩包路径`
```
Package file name: ~/Downloads/citeproc-org-0.2.2.tar

## 2 用法
### 2.1 表格
使用CSDN上的方法可以在文件中 插入[表格](https://blog.csdn.net/dalewzm/article/details/47621089)，用如下的方式可以进行表格命名及参数设置。
```bash
#+CAPTION: 表格标题
#+ATTR_HTML: :boarder 1 :rules row :frame boarder
```
### 2.2 图片
在需要插入图片的部位 C-c C-l 。选取需要插入的图片文件名, 不要在提示中输入图片说明， 否则图片在输出文件中仅为一条链接， 无法显示图片本身。

再在其上方加入图片说明及显示参数：
```bash
#+CAPTION: 图片标题
#+ATTR_HTML: :width 80%
```
## 2.3 参考文献
在文首加入参考文献的格式文件：
```bash
#+CSL_STYLE: /path/to/csl_style_file
```
在文末加入参考文献链接：
```
M-x org-ref-insert-bibliography-link
```
在文中相应位置插入参考文献：
```
M-x org-ref-helm-insert-cite-link
```
## 3 输出
在输出前首先要激活citeproc-org， 才能在输出文件中应用csl样式。
```
M-x citeproc-org-setup
```
输出为html文件
```
C-c C-e h h
```
输出为odt文件
```
C-c C-e o o
```

[https://zhuanlan.zhihu.com/p/54705090](https://zhuanlan.zhihu.com/p/54705090)