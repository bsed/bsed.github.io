---
title: " 如何部署全局git commit-msg hook"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "how-to-deploy-global-git-commitmsg-hook"
date: "2014-01-07 10:42:00"
---

## 问题：
`git`和`gerrit`对接，git commit的时候都提示“missing Change-Id in commit message footer”。知道是因为change-id搞的鬼，也知道从gerrit上或者其他地方拷个commit-msg脚本儿，放到.git/hooks目录下就完事儿了。但是，每次重新克隆时，就有得再来一遍拷贝动作，很低效。

**注：**
单仓拷贝，不用repo。
## 方法：
部署本地全局Hook即可：
```bash
$mkdir -p ~/.git_template/hooks 
$cp commit-msg ~/.git_template/hooks  
$git config --global init.templatedir ~/.git_template  
```