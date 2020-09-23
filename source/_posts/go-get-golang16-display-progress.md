---
title: golang1.6  go get 显示进度
date: 2016-05-29 07:38:00
updated: 2016-05-29 15:40:21
tags: 
- java
- ibatis
categories: 
- java

---
因为leanote在github.com上的包有点大, 所以 `go get github.com/leanote/leanote/app` 会很慢, 这个会执行几分钟或更长, 不知道的朋友还以为卡死了. 找了下 go get 没有一个选项可以输出进度的, 于是决定修改golang源码(别以为很有技术含量, 还不是go代码?).

看了下golang的源码 `src/cmd/go` 下是go命令的源码, 其中, `get.go`是`go get`命令的代码, `build.go` 是`go build`的代码.

刚开始走了点弯路, 想着改变get.go来显示进度, 无果之后想了下, go get 其实就是调用git , hg, svn的命令从仓库中下载的, 由此思路找到vcs.go(vcs全称为version control system), 果然这里面包含了调用git, hg, svn的命令. 问题迎刃而解:


<!--more-->


修改`git clone`命令, 添加 `--progress`选项, 使其输出进度
修改`cmd.Run()`执行的地方, 使其将输出定位到标准输出流上
1. 修改git clone命令, 找到如下代码, 在createdCmd修改为 clone --progress {repo} {dir}

其它命令hg, svn...添加进度方法类似
```golang
// vcsGit describes how to use Git.
var vcsGit = &vcsCmd{
	name: "Git",
	cmd:  "git",

	createCmd:   "clone {repo} {dir}", // 此处修改为 clone --progress {repo} {dir}
	downloadCmd: "pull --ff-only"
}
```
2. 重定向输出流

找到`run1()`方法, 在 `cmd.Stderr = &buf` 下添加两行, 如:
```golang
	var buf bytes.Buffer
	cmd.Stdout = &buf
	cmd.Stderr = &buf
	cmd.Stdout = os.Stdout // 重定向标准输出
	cmd.Stderr = os.Stderr // 重定向标准输出
	err = cmd.Run()
```
Ok, 搞定, 接下来执行golang源码 src下的 `all.bash` 重新编译golang, 编译要些时间, 编译完后使用go get 试试:

![golang_go_get_progress.png][1]
看到进度条就不用担心了吧.


  [1]: https://imgs.gnux.cn/usr/uploads/2016/05/3077150906.png