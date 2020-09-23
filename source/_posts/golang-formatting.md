---
title: Golang fmt格式化和time格式化
date: 2017-06-14 14:38:00
updated: 2017-06-15 14:48:43
tags: 
- golang
- reflect
categories: 
- go

---
项目中总是时不时的要使用`fmt.Printf`或`fmt.Sprintf`来处理某些数据类型,总结一下常用的标识符用途及场景.
## fmt 格式化
```golang
package main
import "fmt"
import "os"
type point struct {
    x, y int
}


<!--more-->


func main() {
//Go 为常规 Go 值的格式化设计提供了多种打印方式。例如，这里打印了 point 结构体的一个实例。
    p := point{1, 2}
    fmt.Printf("%v\n", p) // {1 2}
//如果值是一个结构体，%+v 的格式化输出内容将包括结构体的字段名。
    fmt.Printf("%+v\n", p) // {x:1 y:2}
//%#v 形式则输出这个值的 Go 语法表示。例如，值的运行源代码片段。
    fmt.Printf("%#v\n", p) // main.point{x:1, y:2}
//需要打印值的类型，使用 %T。
    fmt.Printf("%T\n", p) // main.point
//格式化布尔值是简单的。
    fmt.Printf("%t\n", true)
//格式化整形数有多种方式，使用 %d进行标准的十进制格式化。
    fmt.Printf("%d\n", 123)
//这个输出二进制表示形式。
    fmt.Printf("%b\n", 14)
这个输出给定整数的对应字符。
    fmt.Printf("%c\n", 33)
%x 提供十六进制编码。
    fmt.Printf("%x\n", 456)
//对于浮点型同样有很多的格式化选项。使用 %f 进行最基本的十进制格式化。
    fmt.Printf("%f\n", 78.9)
//%e 和 %E 将浮点型格式化为（稍微有一点不同的）科学技科学记数法表示形式。
    fmt.Printf("%e\n", 123400000.0)
    fmt.Printf("%E\n", 123400000.0)
//使用 %s 进行基本的字符串输出。
    fmt.Printf("%s\n", "\"string\"")
//像 Go 源代码中那样带有双引号的输出，使用 %q。
    fmt.Printf("%q\n", "\"string\"")
//和上面的整形数一样，%x 输出使用 base-16 编码的字符串，每个字节使用 2 个字符表示。
    fmt.Printf("%x\n", "hex this")
//要输出一个指针的值，使用 %p。
    fmt.Printf("%p\n", &p)
//当输出数字的时候，你将经常想要控制输出结果的宽度和精度，可以使用在 % 后面使用数字来控制输出宽度。默认结果使用右对齐并且通过空格来填充空白部分。
    fmt.Printf("|%6d|%6d|\n", 12, 345)
//你也可以指定浮点型的输出宽度，同时也可以通过 宽度.精度 的语法来指定输出的精度。
    fmt.Printf("|%6.2f|%6.2f|\n", 1.2, 3.45)
//要最对齐，使用 - 标志。
    fmt.Printf("|%-6.2f|%-6.2f|\n", 1.2, 3.45)
//你也许也想控制字符串输出时的宽度，特别是要确保他们在类表格输出时的对齐。这是基本的右对齐宽度表示。
    fmt.Printf("|%6s|%6s|\n", "foo", "b")
//要左对齐，和数字一样，使用 - 标志。
    fmt.Printf("|%-6s|%-6s|\n", "foo", "b")
//到目前为止，我们已经看过 Printf了，它通过 os.Stdout输出格式化的字符串。Sprintf 则格式化并返回一个字符串而不带任何输出。
    s := fmt.Sprintf("a %s", "string")
    fmt.Println(s)
//你可以使用 Fprintf 来格式化并输出到 io.Writers而不是 os.Stdout。
    fmt.Fprintf(os.Stderr, "an %s\n", "error")
}
```


## time时间日期格式化定义
go 的time package 提供了time.Format函数，用来对时间进行格式化输出;类似的还有time.Parse用来解析字符串类型的时间到time.Time。这是两个互逆的函数。

下面看golang中time包对于时间的详细定义

 - 月份 `1,01,Jan,January`
 - 日　 `2,02,_2`
 - 时　 `3,03,15,PM,pm,AM,am`
 - 分　 `4,04`
 - 秒　 `5,05`
 - 年　 `06,2006`
 - 时区 `-07,-0700,Z0700,Z07:00,-07:00,MST`
 - 周几 `Mon,Monday`

比如小时的表示(原定义是下午3时，也就是15时)

 - `3` 用12小时制表示，去掉前导0
 - `03` 用12小时制表示，保留前导0
 - `15` 用24小时制表示，保留前导0
 - `03pm` 用24小时制am/pm表示上下午表示，保留前导0
 - `3pm` 用24小时制am/pm表示上下午表示，去掉前导0

又比如月份

 - `1` 数字表示月份，去掉前导0
 - `01` 数字表示月份，保留前导0
 - `Jan` 缩写单词表示月份
 - `January` 全单词表示月份

时间日期格式化
本地当期时间
```
fmt.Println(time.Now()) //2016-07-14 14:27:28.214512532 +0800 CST
```
时间格式化
```
fmt.Println(time.Now().Format("3:04:05.000 PM Mon Jan"))            // 2:27:05.702 PM Thu Jul
fmt.Println(time.Now().Format("2006-01-_2 3:04:05.000 PM Mon Jan")) // 2016-07-14 2:54:11.442 PM Thu Jul
fmt.Println(time.Now().Format("2006-01-02 15:04:05"))  // 2016-07-14 14:54:11.442239513 +0800 CST
```
本地当前时间戳(10位)
```
fmt.Println(time.Now().Unix()) //1468479251
```
本地当前时间戳(19位)
```
fmt.Println(time.Now().UnixNano()) //1468480006774460462
```
时间戳转时间
```
fmt.Println(time.Unix(1389058332, 0).Format("2006-01-02 15:04:05")) //2014-01-07 09:32:12
```
时间转时间戳
```
fmt.Println(time.Date(2014, 1, 7, 5, 50, 4, 0, time.Local).Unix())
```
时间转换为UTC时间和本地时间( UTC:零时区 +0000， China: 东八区 +0800)
```golang
dateStr := "2016-07-14 14:24:51" 
timestamp1, _ := time.Parse("2006-01-02 15:04:05", dateStr)
timestamp2, _ := time.ParseInLocation("2006-01-02 15:04:05", dateStr, time.Local)
fmt.Println(timestamp1, timestamp2)               //2016-07-14 14:24:51 +0000 UTC 2016-07-14 14:24:51 +0800 CST 
fmt.Println(timestamp1.Unix(), timestamp2.Unix()) //1468506291 1468477491 

now := time.Now()                
year, mon, day := now.UTC().Date()
hour, min, sec := now.UTC().Clock()
zone, _ := now.UTC().Zone()     
fmt.Printf("UTC 时间是 %d-%d-%d %02d:%02d:%02d %s\n",         
    year, mon, day, hour, min, sec, zone) // UTC 时间是 2016-7-14 07:06:46 UTC
                                                           
year, mon, day = now.Date()
hour, min, sec = now.Clock()
zone, _ = now.Zone()
fmt.Printf("本地时间是 %d-%d-%d %02d:%02d:%02d %s\n",
    year, mon, day, hour, min, sec, zone) // 本地时间是 2016-7-14 15:06:46 CST
```