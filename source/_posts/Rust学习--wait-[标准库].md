---
title: "Rust学习: wait [标准库]"
categories: [ "Rust" ]
tags: [ "rust","标准库","wait" ]
draft: false
slug: "rust-learning-wait-standard-library"
date: "2019-07-19 13:43:00"
---

如果你想等待 `process::Child` 完成，就必须调用 `Child::wait`，这会返回一个 `process::ExitStatus`。


<!--more-->


```rust
use std::process::Command;
fn main() {
    let mut child = Command::new("sleep").arg("5").spawn().unwrap();
    let _result = child.wait().unwrap();
    println!("reached end of main");
}
```
运行:
```bash
$ rustc wait.rs && ./wait
reached end of main
# `wait` keeps running for 5 seconds
# `sleep 5` command ends, and then our `wait` program finishes
```