---
title: "Git单文件恢复"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "git-single-file-recovery-to-the-historical-version"
date: "2015-11-24 09:59:00"
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