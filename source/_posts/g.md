---
title: Git删除分支
date: 2017-10-25 11:55:00
updated: 2017-10-26 11:58:23
tags: 
- php
- parse
categories: 
- php

---
## git删除分支|查看分支动态

git不能在当前分支下删除你当前所在的分支，比如你要删除new分支，而现在正在处于new分支下，则执行git branch -d new的时候会报错

`error: Cannot delete branch 'new' checked out at 'D:/web/webpack_project/front-end'` //提示你要退出当前分支

切换到主分支再执行删除命令 `git checkout master` ; `git branch -d new` 就可以了


<!--more-->


如果new分支和master分支未曾合并过，则会报以下错误

error: The branch 'new' is not fully merged.

执行合并`git merge new` //将new分支合并到master主分支上,合并后要push到远程仓库，这样远程仓库分支的文件就合并到一起了，两个分支的目录跟文件是一样的

合并之后就可以删除new分支了，现在执行`git branch -d new` 就不会报错了

以上是删除本地分支方法，删除远程分支只需推送一个空分支即可 `git push origin :new` 。删除tag也是一样的语法

删除远程分支和tag还有一种语法 `git push --delete branchName`

使用`git branch -v`可以查看本地或远程分支有哪些变化

当提交一个文件未push,`git branch -v`看的是本地分支的变化

当提交一个文件且push成功后,`git branch -v` 查看的是远程分支的变化

`git log` 查看每次commit提交的记录

## 本地分支
查看分支列表

查看本地分支列表
```
$ git branch
master 
gh-pages
```
## 删除本地分支

删除merge了的分支
```bash
$ git branch -d gh-pages
```
删除分支（不管它有没有merge）
$ git branch -D gh-pages
## 远程分支
查看分支列表
查看远程分支列表
```bash
$ git branch -r
origin/HEAD -> origin/master
origin/gh-pages
origin/master
```
查看本地分支与远程分支列表 bash $ git branch -a master gh-pages origin/HEAD -> origin/master origin/gh-pages origin/master
## 删除远程分支

删除远程的foo 分支
```bash
git push --delete origin foo
```
或者
```bash
git push origin :foo
```