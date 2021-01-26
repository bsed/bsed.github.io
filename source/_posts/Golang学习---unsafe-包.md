---
title: "Golang学习 - unsafe 包"
categories: [ "Golang" ]
tags: [ "golang","unsafe" ]
draft: false
slug: "golang-learning-unsafe-pack"
date: "2016-05-19 13:02:00"
---

## 指针类型：

`*`类型：普通指针，用于传递对象地址，不能进行指针运算。

`unsafe.Pointer`：通用指针类型，用于转换不同类型的指针，不能进行指针运算。

`uintptr`：用于指针运算，GC 不把 uintptr 当指针，uintptr 无法持有对象。uintptr 类型的目标会被回收。

 - unsafe.Pointer 可以和 普通指针 进行相互转换。
 - unsafe.Pointer 可以和 uintptr 进行相互转换。


<!--more-->


也就是说 unsafe.Pointer 是桥梁，可以让任意类型的指针实现相互转换，也可以将任意类型的指针转换为 uintptr 进行指针运算。

```golang
// 示例：通过指针修改结构体字段
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	s := struct {
		a byte
		b byte
		c byte
		d int64
	}{0, 0, 0, 0}

	// 将结构体指针转换为通用指针
	p := unsafe.Pointer(&s)
	// 保存结构体的地址备用（偏移量为 0）
	up0 := uintptr(p)
	// 将通用指针转换为 byte 型指针
	pb := (*byte)(p)
	// 给转换后的指针赋值
	*pb = 10
	// 结构体内容跟着改变
	fmt.Println(s)

	// 偏移到第 2 个字段
	up := up0 + unsafe.Offsetof(s.b)
	// 将偏移后的地址转换为通用指针
	p = unsafe.Pointer(up)
	// 将通用指针转换为 byte 型指针
	pb = (*byte)(p)
	// 给转换后的指针赋值
	*pb = 20
	// 结构体内容跟着改变
	fmt.Println(s)

	// 偏移到第 3 个字段
	up = up0 + unsafe.Offsetof(s.c)
	// 将偏移后的地址转换为通用指针
	p = unsafe.Pointer(up)
	// 将通用指针转换为 byte 型指针
	pb = (*byte)(p)
	// 给转换后的指针赋值
	*pb = 30
	// 结构体内容跟着改变
	fmt.Println(s)

	// 偏移到第 4 个字段
	up = up0 + unsafe.Offsetof(s.d)
	// 将偏移后的地址转换为通用指针
	p = unsafe.Pointer(up)
	// 将通用指针转换为 int64 型指针
	pi := (*int64)(p)
	// 给转换后的指针赋值
	*pi = 40
	// 结构体内容跟着改变
	fmt.Println(s)
}
```

结构体成员的内存分配是连续的，第一个成员的地址就是结构体的地址，相对于结构体的偏移量为 0。其它成员都可以通过偏移量来计算其地址。

每种类型都有它的大小和对齐值，可以通过 unsafe.Sizeof 获取其大小，通过 unsafe.Alignof 获取其对齐值，通过 unsafe.Offsetof 获取其偏移量。不过 unsafe.Alignof 获取到的对齐值只是该类型单独使用时的对齐值，不是作为结构体字段时与其它对象间的对齐值，这里用不上，所以需要用 unsafe.Offsetof 来获取字段的偏移量，进而确定其内存地址。

```golang
// 测试：通过反复编译执行下面的代码，观察字段的对齐情况
package main

import (
	"fmt"
	"math/rand"
	"os"
	"time"
)

var types = []string{
	"int", "int8", "int16", "int32", "int64",
	"uint", "uint8", "uint16", "uint32", "uint64",
	"byte", "rune", "uintptr", "bool", "string",
	"float32", "float64", "complex64", "complex128",
	"[]byte", "[]string", "map[string]int",
	"chan int", "func(int) int",
}

var values = []string{
	"0", "0", "0", "0", "0",
	"0", "0", "0", "0", "0",
	"0", "0", "0", "false", "\"\"",
	"0", "0", "0+0i", "0+0i",
	"[]byte{}", "[]string{}", "map[string]int{}",
	"nil", "func(int) int {return 0}",
}

const template1 = `package main

import (
	"fmt"
	"reflect"
)

var v = struct {
		a %v
		b %v
		c %v
		d %v
		e %v
}{%v, %v, %v, %v, %v}
`
const template2 = `
func init() {
	fmt.Printf("%#T\n", v)
    t := reflect.TypeOf(v)
    fmt.Printf("结构体大小：%v\n", t.Size())
    for i := 0; i < t.NumField(); i++ {
        showAlign(t, i)
    }
}

func showAlign(v reflect.Type, i int) {
    sf := v.Field(i)
    fmt.Printf("字段 %10v，大小：%2v，对齐：%2v，字段对齐：%2v，偏移：%2v\n",
        sf.Type.Kind(),
        sf.Type.Size(),
        sf.Type.Align(),
        sf.Type.FieldAlign(),
        sf.Offset,
    )
}`

func main() {
	GetTestFile()
}

func GetTestFile() {
	f, err := os.OpenFile("testAlign.go", os.O_CREATE|os.O_WRONLY|os.O_TRUNC, 0666)
	if err != nil {
		fmt.Println(err)
	}
	defer f.Close()

	t := [5]string{}
	v := [5]string{}
	rand.Seed(time.Now().UnixNano())
	for i := 0; i < 5; i++ {
		n := rand.Intn(len(types))
		t[i] = types[n]
		v[i] = values[n]
	}

	fmt.Fprintf(f, template1,
		t[0], t[1], t[2], t[3], t[4],
		v[0], v[1], v[2], v[3], v[4],
	)
	fmt.Fprint(f, template2)
}
```

修改其它包中的结构体私有字段：

```golang
package main

import (
	"fmt"
	"reflect"
	"strings"
	"unsafe"
)

func main() {
	// 创建一个 strings 包中的 Reader 对象
	// 它有三个私有字段：s string、i int64、prevRune int
	sr := strings.NewReader("abcdef")
	// 此时 sr 中的成员是无法修改的
	fmt.Println(sr)
	// 但是我们可以通过 unsafe 来进行修改
	// 先将其转换为通用指针
	p := unsafe.Pointer(sr)
	// 获取结构体地址
	up0 := uintptr(p)
	// 确定要修改的字段（这里不能用 unsafe.Offsetof 获取偏移量，因为是私有字段）
	if sf, ok := reflect.TypeOf(*sr).FieldByName("i"); ok {
		// 偏移到指定字段的地址
		up := up0 + sf.Offset
		// 转换为通用指针
		p = unsafe.Pointer(up)
		// 转换为相应类型的指针
		pi := (*int64)(p)
		// 对指针所指向的内容进行修改
		*pi = 3 // 修改索引
	}
	// 看看修改结果
	fmt.Println(sr)
	// 看看读出的是什么
	b, err := sr.ReadByte()
	fmt.Printf("%c, %v\n", b, err)
}
```

另外还有一种简单的方法，不用考虑偏移量的问题：
```golang
// 定义一个和 strings 包中的 Reader 相同的本地结构体
type Reader struct {
	s        string
	i        int64
	prevRune int
}

func main() {
	// 创建一个 strings 包中的 Reader 对象
	sr := strings.NewReader("abcdef")
	// 此时 sr 中的成员是无法修改的
	fmt.Println(sr)
	// 我们可以通过 unsafe 来进行修改
	// 先将其转换为通用指针
	p := unsafe.Pointer(sr)
	// 再转换为本地 Reader 结构体
	pR := (*Reader)(p)
	// 这样就可以自由修改 sr 中的私有成员了
	(*pR).i = 3 // 修改索引
	// 看看修改结果
	fmt.Println(sr)
	// 看看读出的是什么
	b, err := sr.ReadByte()
	fmt.Printf("%c, %v\n", b, err)
}
```