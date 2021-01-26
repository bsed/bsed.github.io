---
title: "React Native之Ubuntu环境搭建Nuclide安装与配置"
categories: [ "JS" ]
tags: [ "nodejs","react","apm","nuclide" ]
draft: false
slug: "react-native-ubuntu-environment-to-build-nuclide-installation-and-configuration"
date: "2016-08-16 22:28:00"
---

Facebook开源了能够使用JavaScript开发iOS和Android原生应用的React Native。同时，Facebook还为React Native开发了一款基于跨平台文本编辑器Atom的开源IDE：Nuclide。
Nuclide的官网：[http://nuclide.io](http://nuclide.io)
Nuclide的github：[https://github.com/facebook/nuclide](https://github.com/facebook/nuclide)
Nuclide是Facebook开发的开发React Native的开发工具，基于Github的Atom开发,是基于Atom 的提供的一系列插件集。要使用 Nuclide ，首先需要先安装 `Atom` 。
官网下载地址：(Atom)[https://atom.io/]


<!--more-->
## 安装Atom

下载地址: [https://atom.io/](https://atom.io/)

## 安装方法一：

1、安装完 `Atom` 后，打开 `Settings` 面板，并点击 `Install` 选项卡，然后在搜索框中键入 `nuclide-installer`

选择我们需要安装的插件，点击该插件旁边的蓝色 Install 按钮进行安装。
另一种方法是直接利用 Atom 的包管理器 apm 安装：
```bash
$ apm install nuclide-installer
```
## 安装方法二：
1、还有一种方法，对Nuclide源代码编译安装,
Nuclide项目官方地址: [https://github.com/facebook/nuclide](https://github.com/facebook/nuclide)
```bash
git clone https://github.com/facebook/nuclide.git
cd nuclide
npm install
apm link
```
安装好了就可以高效的进行ReactNative的学习与开放了

使用Nuclide你能够

 - 配合flow进行静态语法检查，自动补全
 - 方便的debug
 - 进行版本控制，方便diff
 - iOS模拟器Log

## 准备工作

本文默认读者已经安装好了 `React Native`，如果没有安装好，可以按照官网的讲解安装，很简单，本文侧重IDE

## 安装react-native
```bash
$ npm install -g react-native-cli
```
使用从官方服务器下载初始项目(推荐下载完成之后备份一份项目文件,因为下载起来很慢)

React Native的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。
如果你看到EACCES: permission denied这样的权限报错，那么需要修复`/usr/local`目录的所有权：
```
sudo chown -R `whoami` /usr/local
```

```bash
$ react-native init ProjectName
```
cd到项目根文件,然后运行项目
```bash
$ cd ProjectName
$ react-native run-ios
```
编辑 `~/ProjectName/index.ios.js` 进行开发

使用`nuclide`开启服务:`Ctrl+Shift+P`, 搜索 `Start Packager`,开启服务

使用终端进行真机测试(ios/android):
```bash
$react-native run-ios
$react-native run-android
```

出现下面的内容， 说明安装成功。
```
BUILD SUCCESSFUL

Total time: 12.827 secs

This build could be faster, please consider using the Gradle Daemon: http://gradle.org/docs/2.4/userguide/gradle_daemon.html
Running /home/kelvin/tools/android/adt-bundle-linux-x86_64-20140702/sdk//platform-tools/adb -s emulator-5554 reverse tcp:8081 tcp:8081
Starting the app on emulator-5554 (/home/kelvin/tools/android/adt-bundle-linux-x86_64-20140702/sdk//platform-tools/adb -s emulator-5554 shell am start -n com.helloworld/.MainActivity)...
Starting: Intent { cmp=com.helloworld/.MainActivity }
```



**注意**，如果没有安装`watchman` 和`Flow`，建议安装

安装`watchman`－自动监听文件内容变化，刷新数据
```bash
brew install watchman
```
如果提示没有安装brew
```bash
/home/kelvin/.rvm/rubies/ruby-2.3.0/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install)"
sudo apt install linuxbrew-wrapper
```
添加配置`.bashrc` or `.zshrc`:
```bash
export PATH="$HOME/.linuxbrew/bin:$PATH"
export MANPATH="$HOME/.linuxbrew/share/man:$MANPATH"
export INFOPATH="$HOME/.linuxbrew/share/info:$INFOPATH"
```
安装`flow`－提供静态语法检查，自动补全
```bash
brew install flow
```
如果你之前安装了`flow`或者`watchman`,建议更新到最新版本

```bash
brew upgrade watchman
brew upgrade flow
```
**注**：

 - [Watchman](https://facebook.github.io/watchman/docs/install.html)
   是由Facebook提供的监视文件系统变更的工具。安装此工具可以提高开发时的性能（packager可以快速捕捉文件的变化从而实现实时刷新）。
 - [Flow](http://www.flowtype.org/)是一个静态的JS类型检查工具。译注：你在很多示例中看到的奇奇怪怪的冒号问号，以及方法参数中像类型一样的写法，都是属于这个flow工具的语法。这一语法并不属于ES标准，只是Facebook自家的代码规范。所以新手可以直接跳过（即不需要安装这一工具，也不建议去费力学习flow相关语法）。

## 插件推荐

atom-beautify 代码格式化
atomerminal 打开终端，pwd为当前文件所在路径
docblockr 写注释的插件
react：React 的语法补全和智能重排；
save-session：让 Atom 记住上一次打开的会话；
browser-plus：在 Atom 中内嵌一个浏览器窗口，方便页面调试（其实 Atom 本身就是一个浏览器）；
react-snippets：React 的代码段；
highlight-selected：高亮当前双击选中的标记；
jshint：检查 JavaScript 的语法，支持 JSX （需要在插件设置中开启 Support Linting JSX）；
emmet：用 emmet （Zen Coding）方式快速编写页面；

必看参考：

- [http://www.jianshu.com/p/765405062caf](http://www.jianshu.com/p/765405062caf)
- [http://blog.csdn.net/hello_hwc/article/details/51612139](http://blog.csdn.net/hello_hwc/article/details/51612139)
- [http://csbun.github.io/blog/2015/12/starting-react-native-with-android/](http://csbun.github.io/blog/2015/12/starting-react-native-with-android/)