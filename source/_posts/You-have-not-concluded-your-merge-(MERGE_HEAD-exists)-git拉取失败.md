---
title: "You have not concluded your merge (MERGE_HEAD exists) git拉取失败"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "you-have-not-concluded-your-merge-mergehead-exists-git-pull-out-failure"
date: "2018-02-07 10:31:00"
---

今天获取git线上仓库代码报了这个错误:

> kelvin@debian:~/workspace/zhtj-api$ git pull You have not concluded your
> merge (MERGE_HEAD exists). Please, commit your changes before you can
> merge.

错误可能是因为在你以前pull下来的代码没有自动合并导致的.

**有2个解决办法**:


<!--more-->


1.保留你本地的修改
```bash
git merge --abort
git reset --merge
```
合并后记得一定要提交这个本地的合并

然后在获取线上仓库
```bash
git pull
```

2.down下线上代码版本,抛弃本地的修改

不建议这样做,但是如果你本地修改不大,或者自己有一份备份留存,可以直接用线上最新版本覆盖到本地
```bash
git fetch --all
git reset --hard origin/master
git fetch
```
