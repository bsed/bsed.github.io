---
title: "Atom Rust开发环境配置"
categories: [ "Linux" ]
tags: [ "atom","racer","rust" ]
draft: false
slug: "rust-atom-development-configuration"
date: "2016-10-15 18:02:00"
---

阅读本文说明你已经安装过了 rust 环境。

## 步骤
安装 language-rust 插件

```bash
apm install language-rust
apm install racer
cargo install racer
```
配置 racer插件settings

<!--more-->

```bash
# Path to Racer executeable
/home/kelvin/.cargo/bin/racer
# Path to Rust source code directory
/home/kelvin/tools/rustc-1.12.0/src
```
## 其他插件

racer – 代码补全
language-rust – 提供语法高亮
linter-rust – 语法检查
rust-api-docs-helper
build-cargo

## 通用插件
terminal-plus
terminal-status
script

## script

`ctrl + shift + P`
输入 script

或者
`ctrl + shift + b`
运行当前程序源文件。

## 基本用法

安装 build-cargo 之后

F7 编译选项
F9 编译

atom 常用快捷键
ctrl + , 打开设置
ctrl + \ 打开/关闭目录栏目
ctrl + w 关闭标签
ctrl + o 打开文件
ctrl + n 新建文件

ctrl + m 括号跳转

alt + left/right 按单词选择
```
'body':
   'ctrl-a up':'pane:split-up'
   'ctrl-a down':'pane:split-down'
   'ctrl-a left':'pane:split-left'
   'ctrl-a right':'pane:split-right'
```

下面的例子演示了如何为ARMv7平台交叉编译rust编译器。
```bash
# 安装Rust，强烈推荐使用rustup.rs来安装。参见https://www.rustup.rs/。
$ curl https://sh.rustup.rs -sSf | sh

# Step 0: 我们的目标平台是ARMv7设备，因此目标三元组是`armv7-unknown-linux-gnueabihf`

# Step 1: 安装C交叉工具链
$ sudo apt-get install -qq gcc-arm-linux-gnueabihf

# Step 2: 安装已经交叉编译得到的标准crates
$ rustup target add armv7-unknown-linux-gnueabihf

# Step 3: Configure cargo for cross compilation
$ mkdir -p ~/.cargo
$ cat >>~/.cargo/config <<EOF
> [target.armv7-unknown-linux-gnueabihf]
> linker = "arm-linux-gnueabihf-gcc"
> EOF

# Test cross compiling a Cargo project
$ cargo new --bin hello
$ cd hello
$ cargo build --target=armv7-unknown-linux-gnueabihf
   Compiling hello v0.1.0 (file:///home/ubuntu/hello)
$ file target/armv7-unknown-linux-gnueabihf/debug/hello
hello: ELF 32-bit LSB  shared object, ARM, EABI5 version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=67b58f42db4842dafb8a15f8d47de87ca12cc7de, not stripped

# Test the binary
$ scp target/armv7-unknown-linux-gnueabihf/debug/hello me@arm:~
$ ssh me@arm:~ ./hello
Hello, world!
```
一、二、三，你现在可以交叉编译啦！


参考: 
- [http://www.codeghar.com/blog/install-rust-from-source-on-linux.html](http://www.codeghar.com/blog/install-rust-from-source-on-linux.html)

- [https://blog.rust-lang.org/2016/05/13/rustup.html](https://blog.rust-lang.org/2016/05/13/rustup.html)