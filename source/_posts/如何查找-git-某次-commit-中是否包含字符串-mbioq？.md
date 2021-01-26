---
title: "如何查找 git 某次 commit 中是否包含字符串 mbioq？"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "how-to-find-a-git-commit-contains-a-string-mbioq"
date: "2014-09-15 10:37:00"
---

```bash
git log --pretty=format:"%h %an %s" | grep "mbioq"
```