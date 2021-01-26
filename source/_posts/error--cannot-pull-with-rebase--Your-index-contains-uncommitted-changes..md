---
title: "error: cannot pull with rebase: Your index contains uncommitted changes."
categories: [ "Linux" ]
tags: [ "git","stash" ]
draft: false
slug: "error-cannot-pull-with-rebaxse-your-index-contains-uncommitted-changes"
date: "2018-04-12 14:36:00"
---

git 执行git pull –rebase报错误如下： 
```bash
error: Cannot pull with rebase: You have unstaged changes. 
error: Additionally, your index contains uncommitted changes. 
```
原因：如果有未提交的更改，是不能git pull的


<!--more-->


解决：
```bash 
先执行git stash 
再执行git pull –rebase 
最后再执行git stash pop
```
- git stash: #可用来暂存当前正在进行的工作 
- git stash pop: #从Git栈中读取最近一次保存的内容