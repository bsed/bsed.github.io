---
title: "zsh compinit: insecure directories, run compaudit for list 解决"
categories: [ "系统" ]
tags: [ "mac",".zshrc" ]
draft: false
slug: "zsh-compinit-insecure-directories-run-compaudit-for-list"
date: "2020-04-28 07:28:00"
---

```bash
source ~/.zshrc 
```
提示如下错误：


<!--more-->


```
zsh compinit: insecure directories, run compaudit for list.
Ignore insecure directories and continue [y] or abort compinit [n]? ncompinit: initialization aborted
complete:13: command not found: compdef

 ls -al ~/.zshrc
-rw-r--r--  1 kelvin  staff  260  4 28 07:20 /Users/kelvin/.zshrc
```
解决办法：
```bash
compaudit 
```

There are insecure directories:
/usr/local/share/zsh/site-functions
/usr/local/shar
e/zsh
再次执行，可以了。
```bash
source ~/.zshrc
```

**解决方案**：

compaudit 找到不安全的文件夹或者文件

修改权限为安全
```
sudo chmod -R 755 文件名
sudo chown -R root:staff 文件名
```