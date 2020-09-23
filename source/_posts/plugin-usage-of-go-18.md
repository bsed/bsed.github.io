---
title: Go 1.8 的 plugin 使用
date: 2017-07-10 15:21:00
updated: 2017-07-11 08:19:55
tags: 
- javascript
- map
categories: 
- js

---
Go 1.8 为我们提供了一个创建共享库的新工具，称为 Plugins！让我们来创建和使用一个插件。 目前的插件只能在 Linux 和 Darwin (1.8 正式版因为 Bug 已移除支持)上工作。

安装 1.8 beta1, 不做说明.

创建一个插件方法到 aplugin.go:

```golang
package main

func Add(x, y int) int {
    return x+y
}

func Subtract(x, y int) int {
    return x-y
}
```


<!--more-->


然后构建插件:

运行下面命令构建插件:

```golang
go build -buildmode=plugin
```

构建指定文件插件 aplugin.go 到 aplugin.so:

```bash
go build -buildmode=plugin -o aplugin.so aplugin.go

```

加载插件:

```bash
p, _ := plugin.Open("./aplugin.so")
//p, err := plugin.Open("./aplugin.so")
```

call 插件:

```
add, _ := p.Lookup("Add")
sub, _ := p.Lookup("Subtract")
```

使用插件:

```bash
sum := add.(func(int, int) int)(11, 2)
fmt.Println(sum)
subt := sub.(func(int, int) int)(11, 2)
fmt.Println(subt)
```

另外源码测试中有:

```
go build -buildmode=c-shared
```

应该可以支持 c 语言构建插件



```golang


```





原文：[[Go 1.8 的 plugin 使用](https://segmentfault.com/a/1190000007713877)](https://segmentfault.com/a/1190000007713877)