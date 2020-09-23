---
title: Git单文件恢复到历史版本
date: 2017-02-07 13:23:00
updated: 2017-02-12 13:24:42
tags: 
- dns
- html
- prefetch
categories: 
- css

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