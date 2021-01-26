---
title: "如何根据commit id找到对应节点所属的分支和标签？"
categories: [ "Linux" ]
tags: [ "git" ]
draft: false
slug: "how-do-you-find-the-branch-and-tag-of-the-corresponding-node-according-to-commit-id"
date: "2014-07-27 10:39:00"
---

```bash
git branch --contains commit  
git tag --contains commit 
```