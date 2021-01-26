---
title: "在Ubuntu 15.04上 使用Atom 和 LLDB 调试 Swift 程序"
categories: [ "IOS" ]
tags: [ "debug","atom","lldb","swift" ]
draft: false
slug: "debugging-the-swift-program-with-atom-in-ubuntu1504"
date: "2016-02-27 14:50:00"
---

> 目前在 Linux 上没有一个可用的 Swift IDE，而且不断地在文本编辑器和终端之间切换让我感觉相当蛋疼。之前从来没用过
> Atom，但我想看看是否不辜负这货的名字。我就带各位一起把 `Swift Package Manager` 和 `LLDB` 装Atom 吧!

原文:[https://medium.com/@Aciid/hacking-atom-to-create-a-swift-ide-that-runs-on-linux-and-mac-c7d9520a0fac#.fzyg41yh9](https://medium.com/@Aciid/hacking-atom-to-create-a-swift-ide-that-runs-on-linux-and-mac-c7d9520a0fac#.fzyg41yh9)

如图：
![atom_swift_01.png][1]


<!--more-->


## Atom Swift Debugger

Atom 是一个 Github 出品的文本编辑器，这货有一个比较屌的称呼：『21世纪的可 Hack 编辑器』。它是用 CoffeeScript（优雅的JavaScript）写的。为 Atom 写 packages 很简单，开发者们被鼓励去自定义这个文本编辑器来满足你的需求。你的 packages 可以有节点依赖(node dependencies)，并且这个项目都是开源的。

[Swift Debugger](https://atom.io/packages/swift-debugger)是一个我写的 atom package。它的功能是执行 `swift build` 命令，然后开启 LLDB。同时在 Atom 的 UI 上就会有编译和 Debug 功能。你也可以可视化地切换断点，LLDB 就会应用这些断点，在断点处停下。

因为Atom可以在 Mac 和 Linux 上工作，那么 swift-debugger package 就也能同时在两个平台上工作啦。

## 教程

我们来创建一个 Swift 小项目，然后在Ubuntu上试试这个 debugger。

首先，如果你还没有 Atom，就先去装一个吧。然后呢，在终端里输入：

```bash
    $ apm install swift-debugger language-swift
    
    Installing swift-debugger to /home/aciid/.atom/packages ✓  
    Installing language-swift to /home/aciid/.atom/packages ✓  
```
apm 就是**Atom Package Manager**的缩写。我们在这里用它来获取最新版本的swift-debugger 并安装它（language-swift 是为 swift 代码提供语法高亮的一个 package）。

现在输入这个来创建一个swift的项目。

```bash
$ mkdir MySwiftProject && touch MySwiftProject/main.swift && touch MySwiftProject/Package.swift
```
用atom命令打开这个文件夹。
```bash
$ atom MySwiftProject
```
![atom_swift_02.png][2]
然后给这个package一个name。把以下代码放在*Package.swift*中并保存.

```swift
import PackageDescription  
let package = Package(  
    name: "MySwiftProject"
)
```
打开*main.swift*并写点啥代码:
```swift
let myAwesomeString = "hey I am an awesome string"  
print(myAwesomeString)  
let awesomeInt = 500  
print(awesomeInt)
```  
按下`alt-r`来激活swift-debugger package。你会看到这样的界面：

![atom_swift_03.png][3]

你会看到编辑器下面有了debugger的窗口出来了。alt-r其实就是用来切换这个窗口的。

现在你需要手动输入将「可执行的名字」和「Swift编译器路径」写在debugger的输入框里。「可执行的名字」是指的你在Package.swift中写的那个name（在本文中，就是MySwiftProject），「Swift编译器的路径」就是指的Swift工具链所在的目录（我的路径是：/home/aciid/swift/swift-2.2-SNAPSHOT-2015-12-01-b-ubuntu15.10/usr/bin）

把上述的两个东西填进输入框中。输入框就是图中写有「po foo」的地方，就像这样（注意，等号前后没有空格）：

```
e=MySwiftProject (按回车)  
p=/home/aciid/swift/swift-2.2-SNAPSHOT-2015–12–01-b-ubuntu15.10/usr/bin (按回车)  
```
这时 debugger 就会分别输出"swift path set"和"executable path set"了。

好嘞，所有都设置好了。这时我们就来编译运行这个项目吧！点 debugger 中的「run」按钮，debugger 就会编译并运行，然后输出在 debugger 窗口上面了。
![atom_swift_04.png][4]

然后我们来设置一个断点吧，光标移动至第三行，然后按 alt-shift-r。你会看到行号那里变蓝了，这就意味着当前行有一个断点了。你可以用这个快捷键来开关这个断点。

现在再点 run，此时程序就会执行到第三行停止，在「po foo」的那个输入框中输入`p myAwesomeString`。

![atom_swift_05.png][5]
点击 "next line" 按钮后, 程序就可以运行到下一行了。点击 "resume" 后就会运行至下一个断点，如果后面没有断点了，就会继续执行。当然，你也可以按 "stop" 来杀掉程序，"clear" 来清除输出。

输入框会将命令直接发送至 LLDB，所以只要是 LLDB 支持的特性，这里都会支持！


现在来试试构建并调试我在[最近一篇文章](https://medium.com/engineering-housing/developing-and-debugging-swift-packages-using-swift-package-manager-a7e4a1c65528#.a7dnq9v1c)中创建的[项目](https://github.com/aciidb0mb3r/SwiftGetClientDemo)。

去终端输:

```bash
$ git clone https://github.com/aciidb0mb3r/SwiftGetClientDemo
$ atom SwiftGetClientDemo
```

我们看到 Package.swift 中的 name 是「GetClientUser」，所以我们输入`e=GetClientUser`以及`p=path/to/swift/toolchain`

点'run',然后你就看到 spm 开始工作咯：
![atom_swift_06.png][6]
![atom_swift_07.png][7]
现在在左边的目录中，找到 Packages 目录，然后打开`Packages/SimpleGetClient-1.0.0/GetClient.swift`，在17行用 `alt-shift-r` 开个断点，然后就 `run` 吧。停到断点上的时候，输入 `p explodedStrings`来查看这个变量。

然后你就可以配合各种像 "next line" 这种按钮来和更多的断点们一起嗨皮了。

如果有什么 bug，可以开个 issue： [https://github.com/aciidb0mb3r/atom-swift-debugger/issues](https://github.com/aciidb0mb3r/atom-swift-debugger/issues)

  [1]: https://imgs.gnux.cn/usr/uploads/2016/02/1245977671.png
  [2]: https://imgs.gnux.cn/usr/uploads/2016/02/1296728914.png
  [3]: https://imgs.gnux.cn/usr/uploads/2016/02/4153722800.png
  [4]: https://imgs.gnux.cn/usr/uploads/2016/02/4180352745.png
  [5]: https://imgs.gnux.cn/usr/uploads/2016/02/948583432.png
  [6]: https://imgs.gnux.cn/usr/uploads/2016/02/1764831052.png
  [7]: https://imgs.gnux.cn/usr/uploads/2016/02/1627740748.png