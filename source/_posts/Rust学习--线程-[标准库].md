---
title: "Rust学习: 线程 [标准库]"
categories: [ "Rust" ]
tags: [ "rust","spawn","标准库" ]
draft: false
slug: "rust-learning-threads-standard-library"
date: "2019-07-12 08:46:14"
---

Rust 通过 `spawn` 函数提供了创建本地操作系统（native OS）线程的机制，该函数的参数是一个转移闭包（moving closure）。


<!--more-->


```rust
use std::thread;
static NTHREADS: i32 = 10;
// 这是主（`main`）线程
fn main() {
    // 提供一个 vector 来存放所创建的子线程（children）。
    let mut children = vec![];
    for i in 0..NTHREADS {
        // 启动（spin up）另一个线程
        children.push(thread::spawn(move || {
            println!("this is thread number {}", i)
        }));
    }
    for child in children {
        // 等待线程到结束。返回一个结果。
        let _ = child.join();
    }
}
```
这些线程由操作系统调度（schedule）。

