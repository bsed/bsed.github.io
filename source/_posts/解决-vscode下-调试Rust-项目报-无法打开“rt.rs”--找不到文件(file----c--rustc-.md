---
title: "解决 vscode下 调试Rust 项目报 无法打开“rt.rs”: 找不到文件(file:///c:/rustc/"
categories: [ "Rust" ]
tags: [ "vscode","rust" ]
draft: false
slug: "solving-rust-project-report-for-debugging-under-vscode"
date: "2019-04-18 13:31:00"
---

笔者在用 windows 10 vscode 调试 rust msvc 项目 项目 提示：

无法打开“rt.rs”: 找不到文件(file:///c:/rustc/3c3d3c1777041200bb7ed7a65b6562d62899778c/src/libstd/rt.rs)。

如图：

![73060-65chkzrwg6o.png](https://imgs.gnux.cn/usr/uploads/2019/04/1780137557.png)


<!--more-->


**解决办法**：

在 对应的盘符下，我的是C盘(`file:///c:/`)。
```bash
cd C:/
mkdir rustc
cd rustc
mkdir 3c3d3c1777041200bb7ed7a65b6562d62899778c
cd 3c3d3c1777041200bb7ed7a65b6562d62899778c
git clone https://github.com/rust-lang/rust.git .
```
如图：
![73150-kz0u1cwzwuc.png](https://imgs.gnux.cn/usr/uploads/2019/04/3665743023.png)

这样就可以继续调试了。

![72011-84sn386qjxy.png](https://imgs.gnux.cn/usr/uploads/2019/04/1134156872.png)

## 扩展

名称: C/C++
id: ms-vscode.cpptools
说明: C/C++ IntelliSense, debugging, and code browsing.
版本: 0.22.1
发布者: Microsoft
VS Marketplace 链接: [https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

名称: Rust (rls)
id: rust-lang.rust
说明: Rust language support - code completion, Intellisense, refactoring, reformatting, errors, snippets. A client for the Rust Language Server, built by the RLS team.
版本: 0.6.1
发布者: rust-lang
VS Marketplace 链接: [https://marketplace.visualstudio.com/items?itemName=rust-lang.rust](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust)



调试配置文件`lanuch.json` ：
```
{
  // 使用 IntelliSense 了解相关属性。 
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "name": "(Windows) Launch",
      "type": "cppvsdbg",
      "request": "launch",
      "program": "${workspaceRoot}/target/debug/hello.exe",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": true,
      "logging": {
        "moduleLoad": false,
        "trace": true
     }
    }
  ]
}
```