---
title: 使用Go Chanel 模拟配置文件热加载
date: 2017-11-02 08:44:00
updated: 2017-11-03 08:42:17
tags: 
- javascript
- jstips
categories: 
- js

---
### Go 信号

Go 语言中的信号量主要是用于Unix/Linux系统，但是也部份兼容Windows。 在标准库中，主要涉及到`os/signal`和`syscall`这两个包。

其中，`os/signal`包主要是提供信号操作函数，而`syscall`里定义了许多信号常量。

### 简单示例

这里先看一段简短的代码：

```go
package main

import (
	"fmt"
	"os"
	"os/signal"
)

func main() {
	c := make(chan os.Signal, 1)
	signal.Notify(c, os.Interrupt)

	// Block until a signal is received.
	s := <-c
	fmt.Println("Got signal:", s) // Got signal: interrupt
}

```

这里是Golang文档中的一个示例，程序会捕获`os.Interrupt`信号，也就是`syscall.SIGINT`信号。 当捕获到这个信号后，将其打印出来。运行这个示例时，按**Ctrl+C**中断程序运行，即可看到输出。


<!--more-->


### 用法详解

在`os/signal`包中，主要有以下几个函数：

```go
// 忽略某个或某几个信号
func Ignore(sig ...os.Signal)
// 注册接收某个或某几个信号
func Notify(c chan<- os.Signal, sig ...os.Signal)
// 重置一个或多个信号处理，如果参数为空，则重置所有信号处理
func Reset(sig ...os.Signal)
// 停止接收信号
func Stop(c chan<- os.Signal)

```

接收处理信号很简单，只需要一个 channel 即可。当接收到信号后，就可以从 channel 中读取到， 然后就可以进行相应处理，与两个 Goroutine 通信类似。

### 用途示例

我们可以利用信号，实现一些动态功能（如不重启刷新加载配置等）。 在使用Nginx时，可以使用`nginx -s reload`来进行配置文件重载，而不中断服务。 这里我们使用Go语言信号量来模拟一下这个功能。

设计流程如下：

1. 捕获发送给程序的`syscall.SIGUSR1`信号量
2. 重新读取配置文件，刷新应用状态

代码如下：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
	"os/signal"
	"syscall"
)

const (
	configFile = "config.txt"
)

func loadConfig() {
	bts, err := ioutil.ReadFile(configFile)
	if err != nil {
		// handle config file error
		fmt.Println(err.Error())
	} else {
		// do something with configurations
		fmt.Printf(string(bts))
	}
}

func listen() {
	c := make(chan os.Signal, 1)
	signal.Notify(c, syscall.SIGUSR1)
	for {
		<-c
		loadConfig()
	}
}

func main() {
	pid := os.Getpid()
	fmt.Printf("PID: %d\n", pid)
	go listen()
	loadConfig()
	for {
	}
}

```

运行(注意这里的两个Shell切换)：

```bash
shell1>> $ ./example
shell1>> PID: 5555
shell1>> foo
shell2>> $ sudo kill -USR1 5555
shell1>> foo
shell2>> $ echo bar > ./config.txt
shell2>> $ sudo kill -USR1 5555
shell1>> bar

```

这里通过使用一个单独的goroutine来监听USR1信号量，如果捕获到了该信号， 就进行配置文件刷新，否则就一直监听。可以看到，当更改配置文件内容后， 发送USR1信号到程序，会引起程序重新加载配置文件，但不重启程序。

### 参考资料

- [Linux Signal及Golang中的信号处理](http://colobu.com/2015/10/09/Linux-Signals/)