---
title: "Git同时提交到多个远程仓"
categories: [ "Linux" ]
tags: [ "github","git" ]
draft: false
slug: "git-submitted-to-multiple-remote-repositories-at-the-same-time"
date: "2021-01-02 19:23:55"
---

想用一次 git 提交到多个仓库，可以通过以下命令实现：
```
# 设置第一个远程仓库
git git remote add origin https://github.com/bsed/icc.git

# 新增一个远程仓库
git remote set-url --add origin https://gitee.com/bsed/icc.git


<!--more-->


# 新增另一个远程仓库
git remote set-url --add origin  https://git.coding.net/bsed/icc.git

# 查看当前远程仓库
git remote -v
此后的只需一次提交，就会将代码提交至三个仓库，如需删除某个仓库，只需执行：

git remote set-url --delete https://git.coding.net/bsed/icc.git
```
即可。

