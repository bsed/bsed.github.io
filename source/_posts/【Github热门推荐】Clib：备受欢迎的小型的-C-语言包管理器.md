---
title: "【Github热门推荐】Clib：备受欢迎的小型的 C 语言包管理器"
categories: [ "日常" ]
tags: [ "clib","github" ]
draft: false
slug: "github-hot-clib"
date: "2014-09-03 13:45:20"
---

    Clib是一套小型的 C 语言的包管理器。Clib的目标是提供独立的“微型” C 程序库，以便开发人员能够快速安装而无需耦合到大的框架。

Clib 是一套小型的 C 语言的包管理器。Clib 的目标是提供独立的“微型” C 程序库，以便开发人员能够快速安装而无需耦合到大的框架。您可以手动复制/粘贴 Clib 到你的项目中，或者您可以在 Clib 的包管理器列表中安装和搜索已经存在记录表中的包——其实就是一个GitHub Wiki 页面。 


<!--more-->


你可以使用 Clib 来获取文件并检查你的存储库，最终用户和贡献者无需安装 Clib。这使得 Clib 可以顺滑地适应任何新的或现有的 C 工作流。

项目托管地址： [https://github.com/clibs/clib](https://github.com/clibs/clib)


##### 安装： #####

homebrew：

	$ brew install clib

Git：

	$ git clone <a href="https://github.com/clibs/clib.git">https://github.com/clibs/clib.git</a> /tmp/clib
	$ cd /tmp/clib
	$ make install

Ubuntu：

	# install libcurl
	$ sudo apt-get install libcurl4-gnutls-dev -qq
	# clone
	$ git clone <a href="https://github.com/clibs/clib.git">https://github.com/clibs/clib.git</a> /tmp/clib && cd /tmp/clib
	# build
	$ make
	# put on path
	$ sudo make install

使用方法：

<b> </b> clib <command> [options]

  Options:

    -h, --help     Output this message
    -v, --version  Output version information

  Commands:

    install [name...]  Install one or more packages
    search [query]     Search for packages
    help <cmd>         Display help for cmd

更多关于 Clib 的介绍请查看 [这里](https://medium.com/code-adventures/b32e6e769cb3)。 