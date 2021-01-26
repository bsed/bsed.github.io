---
title: "E117: Unknown function: pathogen#infect"
categories: [ "Vim" ]
tags: [ "vim","pathnogen" ]
draft: false
slug: "e117-unknown-function-pathogen"
date: "2019-07-04 19:22:00"
---

当我启动Vim时,收到以下错误：
E117: Unknown function: pathogen#infect
E15: Invalid expression: pathogen#infect()


<!--more-->


需要安装pathogen.vim(vim补丁管理器)
1. 解决: [https://github.com/python-mode/python-mode/issues/687](https://github.com/python-mode/python-mode/issues/687)
2. 或见搜Installation

```bash
mkdir -p ~/.vim/autoload ~/.vim/bundle && \
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```
使用方式:

[Vim 補丁管理器：Pathogen](http://blog.leanote.com/post/sanyue9394@163.com/Python%E6%A0%B8%E5%BF%83%E7%BC%96%E7%A8%8B%EF%BC%88%E7%AC%AC%E4%BA%8C%E7%89%88%EF%BC%89)
