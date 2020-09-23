---
title: 似乎另外一个 git 进程在这个仓库中运行，例如：'git commit' 命令打 开了一个编辑器。请确认所有进程都已经关闭然后重试。如果仍然报错
date: 2020-08-10 18:14:00
updated: 2020-08-10 18:15:06
tags: 
- mysql
- mac
- brew
categories: 
- os

---
似乎另外一个 git 进程在这个仓库中运行，例如：'git commit' 命令打 开了一个编辑器。请确认所有进程都已经关闭然后重试。如果仍然报错，

我正在尝试学习如何使用Git，并创建了一个包含HTML，CSS和Javascript文件的小项目。 我从我的基本空项目中创建了一个分支，然后对我的代码进行了一些更改。 我尝试暂存更改，但我收到以下错误消息：

Another git process seems to be running in this repository, e.g.
an editor opened by 'git commit'. Please make sure all processes
are terminated then try again. If it still fails, a git process
may have crashed in this repository earlier:
remove the file manually to continue.


<!--more-->


在应用程序的根目录中使用以下命令。 这将删除index.lock文件并释放活动锁。
```bash
rm .git/index.lock
```