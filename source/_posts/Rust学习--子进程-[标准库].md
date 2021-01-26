---
title: "Rust学习: 子进程 [标准库]"
categories: [ "Rust" ]
tags: [ "rust","标准库","子进程" ]
draft: false
slug: "rust-learning-create-standard-library-1"
date: "2019-07-17 10:53:00"
---

`process::Output` 结构体表示已结束的子进程（child process）的输出，而 `process::Command` 结构体是一个进程创建者（process builder）。


<!--more-->


```rust
use std::process::Command;
fn main() {
    let output = Command::new("rustc")
        .arg("--version")
        .output().unwrap_or_else(|e| {
            panic!("failed to execute process: {}", e)
    });
    if output.status.success() {
        //output由于它似乎返回一个字节字符串，我String::from_utf8_lossy将使用它将其转换为字符。
        let s = String::from_utf8_lossy(&output.stdout);
        print!("rustc succeeded and stdout was:\n{}", s);
    } else {
        let s = String::from_utf8_lossy(&output.stderr);
        print!("rustc failed and stderr was:\n{}", s);
    }
}
```
`String::from_utf8_lossy`：将字节切片转换为字符串，包括无效字符。

（再试试前面的例子，给rustc 命令传入一个错误的标志）

