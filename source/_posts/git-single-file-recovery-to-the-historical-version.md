---
title: Git单文件恢复
date: 2015-11-24 09:59:00
updated: 2016-11-12 10:00:18
tags: 
- centos6
- mirrors
categories: 
- linux

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