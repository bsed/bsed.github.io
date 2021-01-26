---
title: "安装Go语言开发环境"
categories: [ "Golang" ]
tags: [ "go","tutorial" ]
draft: false
slug: "go-tutorial02"
date: "2014-09-05 22:11:00"
---

安装Go语言开发环境实例代码 - 详述Go语言安装所在需要的工作：安装C语言工具，安装Mercurial，更新go到新版本等操作实例。
###安装go环境###
####1、简介####
Go是一个开源项目，采用[BSD授权协议][1]。 该文档介绍如何获取Go源代码，如何编译，以及如何运行Go程序。
<!--more-->
目前有两种方式使用Go语言。这里主要讲述如何使用Go专用的gc系列工具 （6g、8g等）。另一个可选的编译器是 基于gcc后端的 gccgo编译器。关于gccgo的细节清参考[安装并使用gccgo编译器][2] 。

Go编译器可以支持三种指令集。不同体系结构生成的代码质量有一些差别：

amd64 (a.k.a. x86-64); 6g,6l,6c,6a

最成熟的实现，编译器在寄存器级别优化，可以生成高质量的目标代码（有时候gccgo可能更优）。

386 (a.k.a. x86 or x86-32); 8g,8l,8c,8a

amd64平台的的完整移植。

arm (a.k.a. ARM); 5g,5l,5c,5a

在完善中。目前只支持生成Linux的二进制文件，浮点支持比较匮乏，并且生成目标代码时还存在bug。还没有完全通过测试集，也没有任何优化。
除了系统级的接口，go需要的运行时环境对各个平台都是一致的。包含 mark-and-sweep 垃圾内存自动回收（更高效的算法实现正在开发中）， 数组、字符串、智能堆栈 以及 goroutine 等。

目前支持以下系统：FreeBSD、Linux、Native Client 和 OS X (a.k.a. Darwin)。Microsoft Windows 目前正在移植中， 功能还不完整。关于各个系统平台的详细说明，可以参考后面的 [环境变量] 一节。

####2. 安装C语言工具####
Go的工具链采用C语言编写，构建需要安装以下开发工具：

GCC,
C语言标准库,
Bison,
make,
awk, 和
ed （编辑器）.
对于 OS X 系统，以上工具是[Xcode][3]的一部分。

对于 Ubuntu/Debian 系统，运行安装命令： sudo apt-get install bison ed gawk gcc libc6-dev make

####3. 安装Mercurial####
在进行后面的操作之前需要安装Mercurial版本管理系统（可以输出hg名字检测是否安装）。安装输入以下命令：

  `sudo easy_install mercurial`
对于 Ubuntu/Debian 系统，easy_install 命令可以用 apt-get install python-setuptools python-dev build-essential 安装。

如果上述命令安装失败的话，还可以从 [Mercurial Download][4] 下载。

####4. 获取代码####
以下命令会创建一个go目录。切换到相应目录，并且确保当前位置不存在go目录，运行命令：

      `$ hg clone -r release https://go.googlecode.com/hg/ go`
####5. 安装Go####
编译go环境：

    $ cd go/src
      $ ./all.bash

编译完成后，结尾会打印以下信息。

      --- cd ../test
      ---
      Installed Go for linux/amd64 in /home/you/go.
      Installed commands in /home/you/go/bin.
      *** You need to add /home/you/go/bin to your $PATH. ***
      The compiler is 6g.

其中N对于不同的版本会有差异，表示没有通过测试的数目。

####6. 编写程序####
以file.go代码为例，用以下命令编译：

    $ 6g file.go

6g是针对amd64指令的编译器，它的输出文件为 file.6。其中 ‘6’ 表示文件是 amd64指令的输出文件。 如果是386和arm 处理器，后缀则为 8 和 5。 也就是说，如果你用的是386处理器，那么应该用8g命令编译， 输出的文件为file.8。

然后用以下命令连接：

      $ 6l file.6
运行程序：

      $ ./6.out
一个完整的例子：

 

     $ cat >hello.go <<EOF
      package main
      
      import "fmt"
      
      func main() {
              fmt.Printf("hello, world\n")
      }
      EOF
      $ 6g hello.go
      $ 6l hello.6
      $ ./6.out
      hello, world
      $

在连接的时候，没有必要列出hello.6引用的包（这里用到了fmt包）。 连接器（这里是6l）会自动从hello.6文件获取包的引用信息。

如果是编译更复杂的过程，那么可能需要使用Makefile。相关的例子可以 参考 $GOROOT/src/cmd/godoc/Makefile 和 $GOROOT/src/pkg/*/Makefile。

####7. 进一步学习####
开始阅读 [Go语言入门][5] 教程。
参考 [Wiki Codelab][6] 编写一个web程序。
阅读 [Effective Go][7]
阅读 [Go语言文档][8]
####8. 更新go到新版本####
当有新版本发布的时候，会在[Go Nuts][9]邮件列表中通知。可以用以下命令获取最新的发布版本：

    $ cd go/src
      $ hg pull
      $ hg update release
      $ ./all.bash

####9. 社区资源####
在 [Freenode][10] IRC 上，可能有很多#go-nuts的开发人员和用户，你可以获取即时的帮助。

还可以访问Go语言的官方邮件列表 [Go Nuts][11]。

Bug可以在 [Go issue tracker][12] 提交。

对于开发Go语言用户，有令一个专门的邮件列表 [golang-checkins][13]。 这里讨论的是Go语言仓库代码的变更。

如果是中文用户，请访问：[Go语言中文论坛][14]。

####10. 环境变量####
Go编译器需要三个必须的环境变量和一个可选的环境变量。环境变量在.bashrc 或其他配置文件中设置。

$GOROOT

Go安装包的根目录。通常是放在$HOME/go，当然也可以是其他位置。

$GOOS and 和 $GOARCH

这两个环境变量表示目标代码的操作系统和CPU类型。$GOOS选项有linux、 freebsd、darwin (Mac OS X 10.5 or 10.6) 和 nacl (Chrome的Native Client接口，还未完成)。$GOARCH的 选项有amd64 (64-bit x86，目前最成熟)、386 (32-bit x86)、 和arm (32-bit ARM，还未完成)。下面是$GOOS和 $GOARCH的可能组合：

      $GOOS       $GOARCH	
      darwin      386
      darwin      amd64
      freebsd     386
      freebsd     amd64
      linux       386
      linux       amd64
      linux       arm          incomplete
      nacl        386
      windows     386          incomplete

$GOBIN (optional) （可选）

指明用于存放go的二进制程序目录。如果是没设置$GOBIN环境变量， 则默认是安装在$HOME/bin。如果设置了该变量，需要确保$PATH 变量也包含这个路径，这样编译器可以找到正确的执行文件。

$GOARM (optional, arm, default=6)

ARM处理器（待补充）。
需要说明的是$GOARCH和$GOOS环境变量表示的是目标代码 运行环境，和当前使用的平台是无关的。这个对于交叉编译是很方便的。在.bashrc文件中设置以下环境变量：

  

    export GOROOT=$HOME/go
      export GOARCH=amd64
      export GOOS=linux
      export PATH=.:$PATH:$GOBIN

检查是否能正常使用:

      source ~/.bashrc
      cd ~
      8g -V


  [1]: http://golang.org/LICENSE
  [2]: http://golang.org/doc/gccgo_install.html
  [3]: http://developer.apple.com/TOOLS/Xcode/
  [4]: http://mercurial.selenic.com/wiki/Download
  [5]: http://golang.org/doc/go_tutorial.html
  [6]: http://golang.org/doc/codelab/wiki/
  [7]: http://golang.org/doc/effective_go.html
  [8]: http://golang.org/doc/docs.html
  [9]: http://groups.google.com/group/golang-nuts
  [10]: http://freenode.net/
  [11]: http://groups.google.com/group/golang-nuts
  [12]: http://code.google.com/p/go/issues/list
  [13]: http://groups.google.com/group/golang-checkins
  [14]: http://bbs.golang-china.org/