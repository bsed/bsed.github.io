---
title: "Golang学习 - 格式化输入输出"
categories: [ "Golang" ]
tags: [ "golang","format" ]
draft: false
slug: "golang-learning-format-output"
date: "2016-05-05 08:42:00"
---

```golang
【格式化输出】

// 格式化输出：将 arg 列表中的 arg 转换为字符串输出

// 使用动词 v 格式化 arg 列表，非字符串元素之间添加空格
Print(arg列表)
// 使用动词 v 格式化 arg 列表，所有元素之间添加空格，结尾添加换行符
Println(arg列表)
// 使用格式字符串格式化 arg 列表
Printf(格式字符串, arg列表)

// Print 类函数会返回已处理的 arg 数量和遇到的错误信息。


<!--more-->


【格式字符串】

　　格式字符串由普通字符和占位符组成，例如：

"abc%+ #8.3[3]vdef"

　　其中 abc 和 def 是普通字符，其它部分是占位符，占位符以 % 开头（注：%% 将被转义为一个普通的 % 符号，这个不算开头），以动词结尾，格式如下：

%[旗标][宽度][.精度][arg索引]动词

　　方括号中的内容可以省略。


【旗标】

旗标有以下几种：

+   ：对于数值类型总是输出正负号（其它用法在动词部分说明）。

-   ：在右边进行宽度填充，而不是默认的左边。

空格：对于数值类型的正数，保留一个空白的符号位（其它用法在动词部分说明）。

0   ：用 0 进行宽度填充而不用空格，对于数值类型，符号将被移到所有 0 的前面。

#   ：相关用法在动词部分说明。

　　其中 "0" 和 "-" 不能同时使用，优先使用 "-" 而忽略 "0"。


【宽度和精度】

　　“宽度”和“精度”都可以写成以下三种形式：

数值 | * | arg索引*

　　其中“数值”表示使用指定的数值作为宽度值或精度值，“*”表示使用当前正在处理的 arg 的值作为宽度值或精度值，如果这样的话，要格式化的 arg 将自动跳转到下一个。“arg索引*”表示使用指定 arg 的值作为宽度值或精度值，如果这样的话，要格式化的 arg 将自动跳转到指定 arg 的下一个。

　　宽度值：用于设置最小宽度。

　　精度值：对于浮点型，用于控制小数位数，对于字符串或字节数组，用于控制字符数量（不是字节数量）。

　　对于浮点型而言，动词 g/G 的精度值比较特殊，在适当的情况下，g/G 会设置总有效数字，而不是小数位数。


【arg 索引】

　　“arg索引”由中括号和 arg 序号组成（就像上面示例中的 [3]），用于指定当前要处理的 arg 的序号，序号从 1 开始：

'[' + arg序号 + ']'


【动词】

　　“动词”不能省略，不同的数据类型支持的动词不一样。


[通用动词]

v：默认格式，不同类型的默认格式如下：

　　布尔型：t
　　整　型：d
　　浮点型：g
　　复数型：g
　　字符串：s
　　通　道：p
　　指　针：p

#v：默认格式，以符合 Go 语法的方式输出。特殊类型的 Go 语法格式如下：

　　无符号整型：x

T：输出 arg 的类型而不是值（使用 Go 语法格式）。


[布尔型]

t：输出 true 或 false 字符串。


[整型]

b/o/d：输出 2/8/10 进制格式
x/X  ：输出 16 进制格式（小写/大写）
c    ：输出数值所表示的 Unicode 字符
q    ：输出数值所表示的 Unicode 字符（带单引号）。对于无法显示的字符，将输出其转义字符。
U    ：输出 Unicode 码点（例如 U+1234，等同于字符串 "U+%04X" 的显示结果）

对于 o/x/X：
如果使用 "#" 旗标，则会添加前导 0 或 0x。

对于 U：
如果使用 "#" 旗标，则会在 Unicode 码点后面添加相应的 '字符'（前提是该字符必须可显示）


[浮点型和复数型]

b  ：科学计数法（以 2  为底）
e/E：科学计数法（以 10 为底，小写 e/大写 E）
f/F：普通小数格式（两者无区别）
g/G：大指数（指数 >= 6）使用 %e/%E，其它情况使用 %f/%F


[字符串或字节切片]

s  ：普通字符串
q  ：双引号引起来的 Go 语法字符串
x/X：十六进制编码（小写/大写，以字节为元素进行编码，而不是字符）

对于 q：
如果使用了 "+" 旗标，则将所有非 ASCII 字符都进行转义处理。
如果使用了 "#" 旗标，则输出反引号引起来的字符串（前提是
字符串中不包含任何制表符以外的控制字符，否则忽略 # 旗标）

对于 x/X：
如果使用了 " " 旗标，则在每个元素之间添加空格。
如果使用了 "#" 旗标，则在十六进制格式之前添加 0x 前缀。


[指针类型]

p ：带 0x 前缀的十六进制地址值。
#p：不带 0x 前缀的十六进制地址值。


[符合类型]

复合类型将使用不同的格式输出，格式如下：

结　构　体：{字段1 字段2 ...}
数组或切片：[元素0 元素1 ...]
映　　　射：map[键1:值1 键2:值2 ...]

指向符合元素的指针：&{}, &[], &map[]

复合类型本身没有动词，动词将应用到复合类型的元素上。

结构体可以使用 "+v" 同时输出字段名。


【完整示例】

package main

import "fmt"

func main() {
	// 旗标、宽度、精度、索引
	fmt.Printf("|%0+- #[1]*.[2]*[3]d|%0+- #[1]*.[2]*[4]d|\n", 8, 4, 32, 64)

	// 浮点型精度
	fmt.Printf("|%f|%8.4f|%8.f|%.4f|%.f|\n", 3.2, 3.2, 3.2, 3.2, 3.2)
	fmt.Printf("|%.3f|%.3g|\n", 12.345678, 12.345678)
	fmt.Printf("|%.2f|\n", 12.345678+12.345678i)

	// 字符串精度
	s := "你好世界！"
	fmt.Printf("|%s|%8.2s|%8.s|%.2s|%.s|\n", s, s, s, s, s)
	fmt.Printf("|%x|%8.2x|%8.x|%.2x|%.x|\n", s, s, s, s, s)

	// 带引号字符串
	s1 := "Hello 世界!"       // CanBackquote
	s2 := "Hello\n世界!"      // !CanBackquote
	fmt.Printf("%q\n", s1)  // 双引号
	fmt.Printf("%#q\n", s1) // 反引号成功
	fmt.Printf("%#q\n", s2) // 反引号失败
	fmt.Printf("%+q\n", s2) // 仅包含 ASCII 字符

	// Unicode 码点
	fmt.Printf("%U, %#U\n", '好', '好')
	fmt.Printf("%U, %#U\n", '\n', '\n')

	// 接口类型将输出其内部包含的值
	var i interface{} = struct {
		name string
		age  int
	}{"AAA", 20}
	fmt.Printf("%v\n", i)  // 只输出字段值
	fmt.Printf("%+v\n", i) // 同时输出字段名
	fmt.Printf("%#v\n", i) // Go 语法格式

	// 输出类型
	fmt.Printf("%T\n", i)
}


【注意】

1、如果 arg 是一个反射值，则该 arg 将被它所持有的具体值所取代。

2、如果 arg 实现了 Formatter 接口，将调用它的 Format 方法完成格式化。

3、如果 v 动词使用了 # 旗标（%#v），并且 arg 实现了 GoStringer 接口，将调用它的 GoString 方法完成格式化。

如果格式化操作指定了字符串相关的动词（比如 %s、%q、%v、%x、%X），接下来的两条规则将适用：

4。如果 arg 实现了 error 接口，将调用它的 Error 方法完成格式化。

5。如果 arg 实现了 string 接口，将调用它的 String 方法完成格式化。

　　在实现格式化相关接口的时候，要避免无限递归的情况，比如：

type X string

func (x X) String() string {
	return Sprintf("<%s>", x)
}

　　在格式化之前，要先转换数据类型，这样就可以避免无限递归：

func (x X) String() string {
	return Sprintf("<%s>", string(x))
}

　　无限递归也可能发生在自引用数据类型上面，比如一个切片的元素引用了切片自身。这种情况比较罕见，比如：

a := make([]interface{}, 1)
a[0] = a
fmt.Println(a)


【格式化输入】

// 格式化输入：从输入端读取字符串（以空白分隔的值的序列），
// 并解析为具体的值存入相应的 arg 中，arg 必须是变量地址。
// 字符串中的连续空白视为单个空白，换行符根据不同情况处理。
// \r\n 被当做 \n 处理。

// 以动词 v 解析字符串，换行视为空白
Scan(arg列表)
// 以动词 v 解析字符串，换行结束解析
Scanln(arg列表)
// 根据格式字符串中指定的格式解析字符串
// 格式字符串中的换行符必须和输入端的换行符相匹配。
Scanf(格式字符串, arg列表)

// Scan 类函数会返回已处理的 arg 数量和遇到的错误信息。


【格式字符串】

格式字符串类似于 Printf 中的格式字符串，但下面的动词和旗标例外：

p          ：无效
T          ：无效
e/E/f/F/g/G：功能相同，都是扫描浮点数或复数
s/v        ：对字符串而言，扫描一个被空白分隔的子串
#/+        ：无效

对于整型 arg 而言，v 动词可以扫描带有前导 0 或 0x 的八进制或十六进制数值。

宽度被用来指定最大扫描宽度（不会跨越空格），精度不被支持。

如果 arg 实现了 Scanner 接口，将调用它的 Scan 方法扫描相应数据。只有基础类型和实现了 Scanner 接口的类型可以使用 Scan 类方法进行扫描。


【完整示例】

// 对于 Scan 而言，回车视为空白
func main() {
	a, b, c := "", 0, false
	fmt.Scan(&a, &b, &c)
	fmt.Println(a, b, c)
	// 在终端执行后，输入 abc 1 回车 t 回车
	// 结果 abc 1 true
}

// 整型变量也可以解析八进制和十六进制
func main() {
	a, b, c := "", 0, false
	fmt.Scanln(&a, &b, &c)
	fmt.Println(a, b, c)
	// 在终端执行后，输入 def 0x20 T 回车
	// 结果 def 32 true
}

// 格式字符串可以指定宽度
func main() {
	a, b, c := "", 0, false
	fmt.Scanf("%4s%d%t", &a, &b, &c)
	fmt.Println(a, b, c)
	// 在终端执行后，输入 1234567true 回车
	// 结果 1234 567 true
}

// 指定宽度不能跨越空白
func main() {
	a, b, c := "", 0, false
	fmt.Scanf("%4s%d%t", &a, &b, &c)
	fmt.Println(a, b, c)
	// 在终端执行后，输入 12 34567True 回车
	// 结果 12 34567 true
}

// %c 总是匹配下一个字符，包括空格
func main() {
	a, b, c := "", 0, 0
	fmt.Scanf("%s%c%d", &a, &b, &c)
	fmt.Println(a, b, c)
	// 在终端执行后，输入 abc 1 回车
	// 结果 abc 32 1
}


【注意】

　　连续调用 FScan 可能会丢失数据，因为 FScan 中使用了 UnreadRune 对读取的数据进行撤销，而参数 io.Reader 只有 Read 方法，不支持撤销。比如：

package main

import (
	"fmt"
	"io"
	"strings"
)

// 实现 Reader 接口的字符串类型
type stringReader string

func (r *stringReader) Read(b []byte) (n int, err error) {
	n = copy(b, *r)
	*r = (*r)[n:]
	if n == 0 {
		err = io.EOF
	}
	return
}

func main() {
	// 下面的操作将丢失字符 a
	s := stringReader("123abcd")
	a, b := 0, ""
	fmt.Fscan(&s, &a)
	fmt.Fscan(&s, &b)
	fmt.Println(a, b) // 123 bcd

	// 如果使用 strings.NewReader 则不会丢失，因为它实现了 RuneScanner 接口
	r := strings.NewReader("123abcd")
	c, d := 0, ""
	fmt.Fscan(r, &c)
	fmt.Fscan(r, &d)
	fmt.Println(c, d) // 123 abcd
}
```
