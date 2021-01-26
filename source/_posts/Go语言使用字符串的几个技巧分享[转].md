---
title: "Go语言使用字符串的几个技巧分享[转]"
categories: [ "Golang" ]
tags: [ "golang","string" ]
draft: false
slug: "go-uses-several-strings-of-string-to-share"
date: "2017-09-25 12:44:00"
---

## 一、字符串底层就是一个字节数组

这真的非常重要，而且影响着下面的其他几个技巧。当你创建一个字符串时，其本质就是一个字节的数组。这意味着你可以像访问数组一样的访问单独的某个字节。例如，下面的代码逐个打印字符串中的每个字节以及对应字节数组中的每个字节：
```golang
package main
 
import "fmt"
 
func main() { 
  str := "hello"
  for i := 0; i < len(str); i++ {
    fmt.Printf("%b %s\n", str[i], string(str[i]))
  }
}
```
这是一个很重要的知识，因此才有了第二个技巧。。。


<!--more-->


## 二、使用字节数组或缓冲区可以提升字符串连接的速度

在 Go 语言中字符串是只读的，这表示每次当你使用 str = str + "something"时，实际上创建了一个新的字符串对象。如果你寻求代码的最高效率，这里应该使用字节缓冲区来替代，例如：
```golang
package main
 
import ( 
  "bytes"
  "fmt"
)
 
func main() { 
  str := "something"
  buf := bytes.NewBufferString(str)
  for i := 0; i < 1000; i++ {
    buf.Write([]byte(randomString()))
  }
  fmt.Println(buf.String())
}
 
func randomString() string { 
  ret := "pretend-this-is-random"
  return ret
}
```
如果使用字节数组会进一步提升上述代码的效率，但你需要知道最终字符串的大小。一个直观的例子就是 Go 语言中的 left-pad 实现。

## 三、你可以像拼接其他数组一样拼接字符串

当你需要截取字符串中的一部分时，可以使用像截取数组某部分那样来操作，示例代码：
```golang
package main
 
import "fmt"
 
func main() { 
  str := "XBodyContentX"
  content := str[1 : len(str)-1]
  fmt.Println(content)
}
```
## 四、使用 ` 符号来创建多行字符串

这个相当简单，你希望在代码中定义一个包含多行地址信息的字符串，那么你需要用到 ` 这个字符，如下所示：
```golang
package main
 
import "fmt"
 
func main() { 
  str := `Mr. Smith
123 Something St 
Some City, CA 94043` 
  fmt.Println(str)
}
```
## 五、你可以在字符串中嵌入 Unicode 字符

假设实现 WebSocket 通讯时，你需要让传输的数据以 0x00 开始，以 0xFF 结束[源码]

我们可以在任意字符串中通过如下代码实现：
```golang
package main
 
import "fmt"
 
func main() { 
  str := "\x00BodyContent\xff"
  fmt.Println(str)
}
```
同样的，你可以使用 Unicode 字符串来处理，或者也可以在字符串中使用原始字符。例如，下面的代码都是有效的：
```golang
package main
 
import "fmt"
 
func main() { 
  a := "ÿay!"
  b := "\u00FFay!"
  fmt.Println(a, b)
}
```