---
title: Emacs Git的使用
date: 2013-11-11 20:48:00
updated: 2015-09-10 16:46:27
tags: 
- golang
- go1-4
categories: 
- go

---
### 1. 什么是Magit

在介绍Magit之前，我们先来了解一下什么是Git,Git 是 Linux Torvalds 为了帮助管理 Linux® 内核开发而开发的一个开放源码的版本控制软件,它是一个快速、可扩展的分布式版本控制系统，它具有极为丰富的命令集，对内部系统提供了高级操作和完全访问。而这里的Magit只是对Git调用的一个封装，让你在Emacs中方便的使用Git，在Magit中，你可以使用Emacs来检查和修改Git仓库;你可以对tracked文件进行commit操作;你还可以在emacs中浏览修改历史;还有其它等等操作。要注意的是，Magit不是一个Git的完全封装，它只是提供了一些对于Git的常用调用。
![0_1320299220Mm62.gif.png][1]
### 2. Quick Tips


<!--more-->


   * `M-x magit-status` switch to the status buffer of that repository 使用这个命令查看Git的状态，在git中的命令为git status
   * `s` to stage files 把untracked或者unstaged文件的状态改成stage模式，
   * `c` to commit (type in your commit message then C-c C-c to save the message and commit)  把所有staged文件都commit到索引仓库中
   * `P` P to do a git push Update remote refs along with associated objects
   * `F` F to do a git pull Fetch from and merge with another repository or a local branch
   * `TAB`  Toggle hidden status of current section 用来转换显示当前文件的一些信息
   * `i` ignore file this will add the filename to the .gitignore file. 忽略当前文件的版本控制，如一些临时文件
   * `I` ignore file this will add the file to .git/infor/exclude
   * `C-h m` Magit的使用帮助信息


### 3. 参考
[http://www.emacswiki.org/emacs/Magit](http://www.emacswiki.org/emacs/Magit)
[http://philjackson.github.com/magit/](http://philjackson.github.com/magit/) 


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2593057093.png