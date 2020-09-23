---
title: 计算总数 [rust-by-example]
date: 2018-01-18 15:26:00
updated: 2018-12-27 08:54:56
tags: 
- git
- log
categories: 
- linux

---
遍历文件中的每个数字，计算总数。

example:
```
1
2
3
4
5
6
7
8
9
10
=> 55
```


<!--more-->


code:
```rust
use std::fs::File;
use std::io::BufRead;
use std::io::BufReader;

// 计算指定文件中所有数字总数
// 每个数字占用一行
fn sum_file(path: &str) -> i64 {
    let mut sum : i64 = 0;
    let file = match File::open(path){
        Ok(f) => f,
        Err(e) => panic!("不能打开文件 {}: {}",path,e),
    };

    let reader = BufReader::new(file);

    for readline in reader.lines(){
        let line = match readline {
            Ok(readline) => readline,
            Err(e) =>panic!("无法从指定的路径读取 {}: {}", path, e),
        };

        match line.trim().parse::<i64>() {
            Ok(v) => sum +=v,
            Err(_) => panic!("无效的整形<integer> {}: {}", path, line),
        }
    }
    sum
}
fn main() {
        println!("总数: {}", sum_file("../../numbers.txt"));
}

```
结果如图：
![91469-pt3q5hnxcl.png](https://imgs.gnux.cn/usr/uploads/2018/01/1033059153.png)
