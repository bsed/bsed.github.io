---
title: "Git合并错误"
categories: [ "Linux" ]
tags: [ "git","merge" ]
draft: false
slug: "git-merge-error"
date: "2017-10-24 17:37:00"
---

我有一个git分支，称为9-sign-in-out与完美的工作代码，我想把它变成主人。我目前在主分支。
```bash
$ git branch
9-sign-in-out
* master
```
我试图切换到`9-sign-in-out`分支，但它不允许我：


<!--more-->


```bash
$ git checkout 9-sign-in-out
app/helpers/application_helper.rb: needs merge
config/routes.rb: needs merge
error: you need to resolve your current index first
```
任何想法如何可以忽略所有的主分支错误，并将9-sign-out-out分支到主？也许git rebase？但是我不想丢失9-sign-in-out分支中的代码。

值得了解这些错误消息的含义 – 需要合并和错误：您需要解析当前索引首先指示合并失败，并且这些文件中存在冲突。如果你决定，无论你想做什么合并是一个坏主意，毕竟，你可以把事情恢复正常：
```bash
git reset --merge
```
但是，否则你应该解决这些合并冲突，as described in the git manual。

一旦你用任何一种技术处理了，你应该能够签出9签到分支。刚刚重命名你的9登录到主机的问题，如wRAR’s answer建议的是，如果你已经与任何人共享您的以前的主分支，这将为他们创造问题，因为如果两个分支的历史分歧，您将发布重写的历史记录。

基本上你想要做的是将主题分支9-sign-in-out合并到master中，但是在主题分支中保留文件的版本。您可以执行以下步骤：
```bash
# Switch to the topic branch:
git checkout 9-sign-in-out

# Create a merge commit, which looks as if it's merging in from master, but is
# actually discarding everything from the master branch and keeping everything
# from 9-sign-in-out:
git merge -s ours master

# Switch back to the master branch:
git checkout master

# Merge the topic branch into master - this should now be a fast-forward
# that leaves you with master exactly as 9-sign-in-out was:
git merge 9-sign-in-out
```
http://stackoverflow.com/questions/6006737/git-merge-errors