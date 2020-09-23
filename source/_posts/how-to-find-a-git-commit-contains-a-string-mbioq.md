---
title: 如何查找 git 某次 commit 中是否包含字符串 mbioq？
date: 2014-09-15 10:37:00
updated: 2017-01-07 10:38:10
tags: 
- java
categories: 
- java

---
```bash
git log --pretty=format:"%h %an %s" | grep "mbioq"
```