---
title: 如何理解Rust的默认线程安全 [转]
date: 2020-01-09 14:56:00
updated: 2020-01-16 13:29:16
tags: 
- typecho
- php
categories: 
- php

---
![mbioq_rust_refcell_ref.png][1]
本文以`Rc`和`RefCell`为例，讨论Rust中的`Send`和`Sync`是如何保证线程安全的。

### 基本概念

`Send`和`Sync`位于标准库`std::marker`模块中。它们属于标记trait，也就是说，它们没有方法，也没有内置任何功能。它们的作用是：

- 如果类型`T`实现了`Send`，则将类型`T`的值传递给另一个线程不会导致数据争用(data rases)或其他不安全性
- 如果类型`T`实现了`Sync`，则将类型`T`的引用`＆T`传递到另一个线程中不会导致数据争用或其他不安全性（`T: Sync`暗含着`＆T: Send`）

也就是说，`Sync`与类型跨多个线程共享时有关，而`Send`则讨论类型被move到另一个线程的行为方式。

<!--more-->

> Allowing Transference of Ownership Between Threads with Send `Send`允许在线程间转移所有权

> Allowing Access from Multiple Threads with Sync `Sync`允许多线程访问

在Rust的标准库`std::marker`模块中，为所有类型默认实现了`Send`和`Sync`。

### 线程

Rust与线程相关的内容位于标准库`std::thread`模块中。Rust中的线程，是对操作系统线程的直接封装。也就是说是本地线程，每个线程都有自己的栈和本地状态。

```rust
use std::thread;
use std::time::Duration;

fn main() {
	 // 使用Builder模式为创建的线程t指定一些参数
    let t = thread::Builder::new()
        .name("t".to_string())
        // 创建线程t，使用move转移所有权
        .spawn(move || {
        	  // 线程t的执行逻辑
            println!("enter child thread.");
            // 暂停当前线程，进入等待状态
            thread::park();
            println!("resume child thread");
        }).unwrap();
    println!("spawn a thread");
    // 使当前线程等待一段时间继续执行
    thread::sleep(Duration::new(5,0));
    // 恢复线程t的执行
    t.thread().unpark();
    // 主线程等待线程t结束
    t.join();
    println!("child thread finished");
}
```

`std::thread::spawn`函数的函数签名如下：

```rust
pub fn spawn<F, T>(f: F) -> JoinHandle<T> 
where F: FnOnce() -> T, F: Send + 'static, T: Send + 'static
```

通过函数签名我们可以看出，spawn()接受一个可调用的（通常是一个闭包），其被调用一次，并包含`Send`和`'static`的数据。也就是说只有实现了`Send`的类型才可以在线程间传递。

同时`'static`限定阻止线程之间共享借用的数据。闭包可以捕获外部变量，但默认情况下它是通过引用捕获的。示例代码中如果没有`move`关键字，则闭包将不会是`'static`的，因为它包含借用的数据。

### `Rc`和`RefCell`示例

线程间传递可变字符串。

`Rc`表示“Reference Counted”（引用计数），单线程引用计数指针。

```rust
use std::thread;
use std::rc::Rc;

fn main() {
    let mut s = Rc::new("example".to_string());
    for _ in 0..2 {
        let mut s_clone = s.clone();
        thread::spawn(move || {
            s_clone.push_str(" Send and Sync!");
        });
    }
}
```

编译会报错，错误信息告诉我们，`std::rc::Rc`无法在线程之间安全地发送。

这是因为`Rc`没有实现`Send`。我们可以使用`Arc`来共享所有权。

```rust
use std::thread;
//use std::rc::Rc;
use std::sync::Arc;

fn main() {
    //let mut s = Rc::new("example".to_string());
    let s = Arc::new("example".to_string());
    for _ in 0..2 {
        //let mut s_clone = s.clone();
        let s_clone = s.clone();
        thread::spawn(move || {
            s_clone.push_str(" Send and Sync!");
        });
    }
}
```

编译还是报错，错误信息告诉我们，把不可变借用当作可变借用了。

这是因为`Arc`默认是不可变的。我们可以使用之前文章中提到的具有内部可变性的类型。

`RefCell`表示可变的内存位置，运行时检查借用规则。

```rust
use std::thread;
//use std::rc::Rc;
use std::sync::Arc;
use std::cell::RefCell;

fn main() {
    //let mut s = Rc::new("example".to_string());
    let s = Arc::new(RefCell::new("example".to_string()));
    for _ in 0..2 {
        //let mut s_clone = s.clone();
        let s_clone = s.clone();
        thread::spawn(move || {
            let s_clone = s_clone.borrow_mut();
            s_clone.push_str(" Send and Sync!");
        });
    }
}
```

编译再次报错，错误信息告诉我们，`std::cell::RefCell`无法在线程之间安全地共享。

这是因为`RefCell`没有实现`Sync`。

### 结语
Rust通过Send和Sync这两个标记trait，将类型贴上“标签”，由编译器识别类型是否可以在多个线程之间移动或共享，在编译期间发现问题，消除数据竞争，从而保证线程安全。

原文：[https://rust-china.org/article?id=d88ad6b9-afbb-473d-9a48-cbde4e9ec45f](https://rust-china.org/article?id=d88ad6b9-afbb-473d-9a48-cbde4e9ec45f)


  [1]: https://imgs.gnux.cn/usr/uploads/2020/01/4070943787.png