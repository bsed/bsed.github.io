---
title: "同一个程序的几种语言版本对照：Haskell, OCaml, Nimrod, Zimbu, Chicken"
categories: [ "Linux" ]
tags: [ "zimbu","haskell","ocaml","nimord","chicken" ]
draft: false
slug: "linux-haskell-ocaml-nimrod-zimbu-chickenscheme"
date: "2014-12-30 15:57:00"
---

此程序用于本社区仓库自动取回仓库服务器，实际使用时带有 set-user-id 标志。其功能完全一样：
[http://p.vim-cn.com/cbf0/haskell](http://p.vim-cn.com/cbf0/haskell) 26 行
[http://p.vim-cn.com/cbfR/ocaml](http://p.vim-cn.com/cbfR/ocaml) 18 行
[http://p.vim-cn.com/cbfX/nimrod](http://p.vim-cn.com/cbfX/nimrod) 26 行
[http://p.vim-cn.com/cbfW/text(Zimbu)](http://p.vim-cn.com/cbfW/text) 35 行
[http://p.vim-cn.com/cbfZ/go](http://p.vim-cn.com/cbfZ/go) 44 行
[http://p.vim-cn.com/cbvS/scheme](http://p.vim-cn.com/cbvS/scheme)(Chicken) 46 行


<!--more-->


编译命令：

    # 不开启优化时为 786K
    ghc -O2 removepkg
    
    # 默认为速度优化，实际上为大小优化时差异很小
    ocamlopt removepkg.ml -o removepkg
    
    # -Os。为速度优化（-O3）时为 211K。不优化时 180K
    nimrod c --opt:size removepkg.nim
    
    zimbu --ccarg -O2 removepkg.zu
    
    go build removepkg.go
    
    csc -optimize-level 3 removepkg.scm

strip 之后的程序大小：

    Haskell: 782K
    OCaml: 289K
    Nimrod: 144K
    Zimbu: 84K
    Go: 1.2M
    Chicken (Scheme): 18K

文档情况：
Haskell: 列表、按函数名子字符串搜索、按类型模糊搜索（Hoogle）
OCaml: 列表、按函数名搜索、按类型搜索（[http://search.ocaml.jp/](http://search.ocaml.jp/)）
Nimrod: 列表、使用 Google 搜索
附：欢迎提交功能完全一致的其它可编译为二进制程序的语言版本。

附上原文把：[https://bbs.archlinuxcn.org/viewtopic.php?id=2131](https://bbs.archlinuxcn.org/viewtopic.php?id=2131)