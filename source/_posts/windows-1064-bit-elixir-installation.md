---
title: windows 10 64位 elixir安装
date: 2017-05-21 12:49:00
updated: 2017-05-22 12:49:46
tags: 
- golang
categories: 
- go

---
## 1.卸载旧的erlang（可选）

## 2.安装新的erlang OTP18

https://packages.erlang-solutions.com/erlang/esl-erlang/FLAVOUR_1_general/esl-erlang_18.3-1~windows_amd64.exe 


<!--more-->


## 3.下载并运行elixir web 安装工具（不用）

https://s3.amazonaws.com/s3.hex.pm/elixir-websetup.exe 

## 4.直接下载precompile版本

https://github.com/elixir-lang/elixir/releases/ 
解压缩到c:elixir目录

## 5.添加到环境变量

D:\erl7.3\bin;D:\elixir\bin;

## 6.验证

erlang:
```
C:\Users\feng>erl
Eshell V7.3  (abort with ^G)
1>
```
elixir:
```
C:\Users\feng>iex
Eshell V7.3  (abort with ^G)
Interactive Elixir (1.4.4) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> 3+5
8
iex(2)>
```
ok.