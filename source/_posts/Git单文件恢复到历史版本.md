---
title: "Git单文件恢复到历史版本"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "git-single-file-restore-to-history"
date: "2017-02-07 13:23:00"
---

查看commit_id
```bash
git log $filename  
```
重置文件
```bash
git reset $commit_id  
```
恢复文件
```bash
git checkout $filename  
```