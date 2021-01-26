---
title: "mac 下安装 CMake-3.18.4"
categories: [ "C/Cpp" ]
tags: [ "mac","c++","cmake" ]
draft: false
slug: "install-cmake3184-under-mac"
date: "2020-10-08 19:48:00"
---

## 官网下载CMake：
传送门：[https://cmake.org/download/](https://cmake.org/download/)

![mac_CMAKE_3.18.4.jpg][1]

## 安装完打开：


<!--more-->


![Jietu20201008-194749.jpg][2]
选择Tools-->How to install Fow Command Line Use

弹窗里提供了命令行使用CMake的配置方法

临时使用（关闭命令行窗口会失效）：
```bash
PATH="/Applications/CMake.app/Contents/bin":"$PATH"
```
长期使用：
```bash
sudo "/Applications/CMake.app/Contents/bin/cmake-gui" --install
```
查看版本检验是否成功：
```bash
cmake --version
```


  [1]: https://imgs.gnux.cn/usr/uploads/2020/10/883603271.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2020/10/1392250219.jpg