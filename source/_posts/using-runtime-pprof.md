---
title: 使用 runtime/pprof 后 有时候会看到 prof 文件是空的
date: 2019-09-08 09:32:00
updated: 2019-09-08 09:36:29
tags: 
- tensorflow
- 斗大的熊猫
categories: 
- bigdata

---
> CPU消耗分析
使用 runtime/pprof 做应用程序性能监控

<!--more-->


```golang
import  "runtime/pprof"

func main() {
    f, err := os.OpenFile("./tmp/cpu.prof", os.O_RDWR|os.O_CREATE, 0644)
    if err != nil {
        log.Fatal(err)
    }
    defer f.Close()
    pprof.StartCPUProfile(f)
    defer pprof.StopCPUProfile()

	// 注意，有时候 defer f.Close()， defer pprof.StopCPUProfile() 会执行不到，这时候我们就会看到 prof 文件是空的， 我们需要在自己代码退出的地方，增加上下面两行，确保写文件内容了。
	pprof.StopCPUProfile()
	f.Close()
}
```
`echo framework` 可以用  使用 [https://github.com/sevennt/echo-pprof/](https://github.com/sevennt/echo-pprof/)
参考： [https://studygolang.com/articles/7069]（https://studygolang.com/articles/7069）