---
title: Golang学习 - reflect 包
date: 2016-10-01 10:39:00
updated: 2017-02-20 10:43:16
tags: 
- golang
- format
categories: 
- go

---
　　在 reflect 包中，主要通过两个函数 TypeOf() 和 ValueOf() 实现反射，TypeOf() 获取到的结果是 reflect.Type 类型，ValueOf() 获取到的结果是 reflect.Value 类型，这两种类型都有很多方法可以进一步获取相关的反射信息。

　　这里有一个函数，可以获取指定对象的所有字段和方法：

------------------------------
```golang
// 获取一个对象的字段和方法
package main

import (
	"fmt"
	"reflect"
)

// 获取一个对象的字段和方法
func GetMembers(i interface{}) {
	// 获取 i 的类型信息
	t := reflect.TypeOf(i)

	for {
		// 进一步获取 i 的类别信息
		if t.Kind() == reflect.Struct {
			// 只有结构体可以获取其字段信息
			fmt.Printf("\n%-8v %v 个字段:\n", t, t.NumField())
			// 进一步获取 i 的字段信息
			for i := 0; i < t.NumField(); i++ {
				fmt.Println(t.Field(i).Name)
			}
		}
		// 任何类型都可以获取其方法信息
		fmt.Printf("\n%-8v %v 个方法:\n", t, t.NumMethod())
		// 进一步获取 i 的方法信息
		for i := 0; i < t.NumMethod(); i++ {
			fmt.Println(t.Method(i).Name)
		}
		if t.Kind() == reflect.Ptr {
			// 如果是指针，则获取其所指向的元素
			t = t.Elem()
		} else {
			// 否则上面已经处理过了，直接退出循环
			break
		}
	}
}

// 定义一个结构体用来进行测试
type sr struct {
	string
}

// 接收器为实际类型
func (s sr) Read() {
}

// 接收器为指针类型
func (s *sr) Write() {
}

func main() {
	// 测试
	GetMembers(&sr{})
}

/* 测试结果（可以读取私有字段）：
*main.sr 2 个方法:
Read
Write

main.sr  1 个字段:
string

main.sr  1 个方法:
Read
*/

------------------------------

　　我们可以通过下面的代码获取 reflect.Type 的所有方法，以便进行学习：

// reflect.Type 是一个接口类型
GetMembers(new(reflect.Type))

　　列出的方法并不一定通用，需要根据不同的类型选择使用，我们在这里给它们分一下类：

------------------------------

// 通用

// 获取 t 类型的字符串描述，不要通过 String 来判断两种类型是否一致。
func (t *rtype) String() string

// 获取 t 类型在其包中定义的名称，未命名类型则返回空字符串。
func (t *rtype) Name() string

// 获取 t 类型所在包的名称，未命名类型则返回空字符串。
func (t *rtype) PkgPath() string

// 获取 t 类型的类别。
func (t *rtype) Kind() reflect.Kind

// 获取 t 类型的值在分配内存时的大小，功能和 unsafe.SizeOf 一样。
func (t *rtype) Size() uintptr

// 获取 t 类型的值在分配内存时的字节对齐值。
func (t *rtype) Align() int

// 获取 t 类型的值作为结构体字段时的字节对齐值。
func (t *rtype) FieldAlign() int

// 获取 t 类型的方法数量。
func (t *rtype) NumMethod() int

// 根据索引获取 t 类型的方法，如果方法不存在，则 panic。
// 如果 t 是一个实际的类型，则返回值的 Type 和 Func 字段会列出接收者。
// 如果 t 只是一个接口，则返回值的 Type 不列出接收者，Func 为空值。
func (t *rtype) Method() reflect.Method

// 根据名称获取 t 类型的方法。
func (t *rtype) MethodByName(string) (reflect.Method, bool)

// 判断 t 类型是否实现了 u 接口。
func (t *rtype) Implements(u reflect.Type) bool

// 判断 t 类型的值可否转换为 u 类型。
func (t *rtype) ConvertibleTo(u reflect.Type) bool

// 判断 t 类型的值可否赋值给 u 类型。
func (t *rtype) AssignableTo(u reflect.Type) bool

// 判断 t 类型的值可否进行比较操作
func (t *rtype) Comparable() bool

------------------------------

// 示例
type inf interface {
	Method1()
	Method2()
}

type ss struct {
	a func()
}

func (i ss) Method1() {}
func (i ss) Method2() {}

func main() {
	s := reflect.TypeOf(ss{})
	i := reflect.TypeOf(new(inf)).Elem()

	Test(s)
	Test(i)
}

func Test(t reflect.Type) {
	if t.NumMethod() > 0 {
		fmt.Printf("\n--- %s ---\n", t)
		fmt.Println(t.Method(0).Type)
		fmt.Println(t.Method(0).Func.String())
	}
}

// 输出结果：
// --- main.ss ---
// func(main.ss)
// <func(main.ss) Value>
//
// --- main.inf ---
// func()
// <invalid Value>

------------------------------

// 数值

// 获取数值类型的位宽，t 必须是整型、浮点型、复数型
func (t *rtype) Bits() int

------------------------------

// 数组

// 获取数组的元素个数
func (t *rtype) Len() int

------------------------------

// 映射

// 获取映射的键类型
func (t *rtype) Key() reflect.Type

------------------------------

// 通道

// 获取通道的方向
func (t *rtype) ChanDir() reflect.ChanDir

------------------------------

// 结构体

// 获取字段数量
func (t *rtype) NumField() int

// 根据索引获取字段
func (t *rtype) Field(int) reflect.StructField

// 根据名称获取字段
func (t *rtype) FieldByName(string) (reflect.StructField, bool)

// 根据指定的匹配函数 math 获取字段
func (t *rtype) FieldByNameFunc(match func(string) bool) (reflect.StructField, bool)

// 根据索引链获取嵌套字段
func (t *rtype) FieldByIndex(index []int) reflect.StructField

------------------------------

// 函数

// 获取函数的参数数量
func (t *rtype) NumIn() int

// 根据索引获取函数的参数信息
func (t *rtype) In(int) reflect.Type

// 获取函数的返回值数量
func (t *rtype) NumOut() int

// 根据索引获取函数的返回值信息
func (t *rtype) Out(int) reflect.Type

// 判断函数是否具有可变参数。
// 如果有可变参数，则 t.In(t.NumIn()-1) 将返回一个切片。
func (t *rtype) IsVariadic() bool

------------------------------

// 数组、切片、映射、通道、指针、接口

// 获取元素类型、获取指针所指对象类型，获取接口的动态类型
func (t *rtype) Elem() reflect.Type

------------------------------

　　下面的代码用到了所有这些方法：

------------------------------

// 获取各种类型的相关信息
package main

import (
	"fmt"
	"reflect"
	"unsafe"
)

// 嵌套结构体
type ss struct {
	a struct {
		int
		string
	}
	int
	string
	bool
	float64
}


<!--more-->


func (s ss) Method1(i int) string  { return "结构体方法1" }
func (s *ss) Method2(i int) string { return "结构体方法2" }

var (
	intValue   = int(0)
	int8Value  = int8(8)
	int16Value = int16(16)
	int32Value = int32(32)
	int64Value = int64(64)

	uIntValue   = uint(0)
	uInt8Value  = uint8(8)
	uInt16Value = uint16(16)
	uInt32Value = uint32(32)
	uInt64Value = uint64(64)

	byteValue    = byte(0)
	runeValue    = rune(0)
	uintptrValue = uintptr(0)

	boolValue   = false
	stringValue = ""

	float32Value = float32(32)
	float64Value = float64(64)

	complex64Value  = complex64(64)
	complex128Value = complex128(128)

	arrayValue  = [5]string{}           // 数组
	sliceValue  = []byte{0, 0, 0, 0, 0} // 切片
	mapValue    = map[string]int{}      // 映射
	chanValue   = make(chan int, 2)     // 通道
	structValue = ss{                   // 结构体
		struct {
			int
			string
		}{10, "子结构体"},
		20,
		"结构体",
		false,
		64.0,
	}

	func1Value = func(a, b, c int) string { // 函数（固定参数）
		return fmt.Sprintf("固定参数：%v %v %v", a, b, c)
	}

	func2Value = func(a, b int, c ...int) string { // 函数（动态参数）
		return fmt.Sprintf("动态参数：%v %v %v", a, b, c)
	}

	unsafePointer     = unsafe.Pointer(&structValue)    // 通用指针
	reflectType       = reflect.TypeOf(0)               // 反射类型
	reflectValue      = reflect.ValueOf(0)              // 反射值
	reflectArrayValue = reflect.ValueOf([]int{1, 2, 3}) // 切片反射值
	// 反射接口类型
	interfaceType = reflect.TypeOf(new(interface{})).Elem()
)

// 简单类型
var simpleTypes = []interface{}{
	intValue, &intValue, // int
	int8Value, &int8Value, // int8
	int16Value, &int16Value, // int16
	int32Value, &int32Value, // int32
	int64Value, &int64Value, // int64
	uIntValue, &uIntValue, // uint
	uInt8Value, &uInt8Value, // uint8
	uInt16Value, &uInt16Value, // uint16
	uInt32Value, &uInt32Value, // uint32
	uInt64Value, &uInt64Value, // uint64
	byteValue, &byteValue, // byte
	runeValue, &runeValue, // rune
	uintptrValue, &uintptrValue, // uintptr
	boolValue, &boolValue, // bool
	stringValue, &stringValue, // string
	float32Value, &float32Value, // float32
	float64Value, &float64Value, // float64
	complex64Value, &complex64Value, // complex64
	complex128Value, &complex128Value, // complex128
}

// 复杂类型
var complexTypes = []interface{}{
	arrayValue, &arrayValue, // 数组
	sliceValue, &sliceValue, // 切片
	mapValue, &mapValue, // 映射
	chanValue, &chanValue, // 通道
	structValue, &structValue, // 结构体
	func1Value, &func1Value, // 定参函数
	func2Value, &func2Value, // 动参函数
	structValue.Method1, structValue.Method2, // 方法
	unsafePointer, &unsafePointer, // 指针
	reflectType, &reflectType, // 反射类型
	reflectValue, &reflectValue, // 反射值
	interfaceType, &interfaceType, // 接口反射类型
}

// 空值
var unsafeP unsafe.Pointer

// 空接口
var nilInterfece interface{}

func main() {
	// 测试简单类型
	for i := 0; i < len(simpleTypes); i++ {
		PrintInfo(simpleTypes[i])
	}
	// 测试复杂类型
	for i := 0; i < len(complexTypes); i++ {
		PrintInfo(complexTypes[i])
	}
	// 测试单个对象
	PrintInfo(unsafeP)
	PrintInfo(&unsafeP)
	PrintInfo(nilInterfece)
	PrintInfo(&nilInterfece)
}

func PrintInfo(i interface{}) {
	if i == nil {
		fmt.Println("--------------------")
		fmt.Printf("无效接口值：%v\n", i)
		return
	}
	t := reflect.TypeOf(i)
	PrintType(t)
}

func PrintType(t reflect.Type) {
	fmt.Println("--------------------")
	// ----- 通用方法 -----
	fmt.Println("String             :", t.String())     // 类型字符串
	fmt.Println("Name               :", t.Name())       // 类型名称
	fmt.Println("PkgPath            :", t.PkgPath())    // 所在包名称
	fmt.Println("Kind               :", t.Kind())       // 所属分类
	fmt.Println("Size               :", t.Size())       // 内存大小
	fmt.Println("Align              :", t.Align())      // 字节对齐
	fmt.Println("FieldAlign         :", t.FieldAlign()) // 字段对齐
	fmt.Println("NumMethod          :", t.NumMethod())  // 方法数量
	if t.NumMethod() > 0 {
		i := 0
		for ; i < t.NumMethod()-1; i++ {
			fmt.Println("    ┣", t.Method(i).Name) // 通过索引定位方法
		}
		fmt.Println("    ┗", t.Method(i).Name) // 通过索引定位方法
	}
	if sm, ok := t.MethodByName("String"); ok { // 通过名称定位方法
		fmt.Println("MethodByName       :", sm.Index, sm.Name)
	}
	fmt.Println("Implements(i{})    :", t.Implements(interfaceType))  // 是否实现了指定接口
	fmt.Println("ConvertibleTo(int) :", t.ConvertibleTo(reflectType)) // 是否可转换为指定类型
	fmt.Println("AssignableTo(int)  :", t.AssignableTo(reflectType))  // 是否可赋值给指定类型的变量
	fmt.Println("Comparable         :", t.Comparable())               // 是否可进行比较操作
	// ----- 特殊类型 -----
	switch t.Kind() {
	// 指针型：
	case reflect.Ptr:
		fmt.Println("=== 指针型 ===")
		// 获取指针所指对象
		t = t.Elem()
		fmt.Printf("转换到指针所指对象 : %v\n", t.String())
		// 递归处理指针所指对象
		PrintType(t)
		return
	// 自由指针型：
	case reflect.UnsafePointer:
		fmt.Println("=== 自由指针 ===")
		// ...
	// 接口型：
	case reflect.Interface:
		fmt.Println("=== 接口型 ===")
		// ...
	}
	// ----- 简单类型 -----
	// 数值型：
	if reflect.Int <= t.Kind() && t.Kind() <= reflect.Complex128 {
		fmt.Println("=== 数值型 ===")
		fmt.Println("Bits               :", t.Bits()) // 位宽
	}
	// ----- 复杂类型 -----
	switch t.Kind() {
	// 数组型：
	case reflect.Array:
		fmt.Println("=== 数组型 ===")
		fmt.Println("Len                :", t.Len())  // 数组长度
		fmt.Println("Elem               :", t.Elem()) // 数组元素类型
	// 切片型：
	case reflect.Slice:
		fmt.Println("=== 切片型 ===")
		fmt.Println("Elem               :", t.Elem()) // 切片元素类型
	// 映射型：
	case reflect.Map:
		fmt.Println("=== 映射型 ===")
		fmt.Println("Key                :", t.Key())  // 映射键
		fmt.Println("Elem               :", t.Elem()) // 映射值类型
	// 通道型：
	case reflect.Chan:
		fmt.Println("=== 通道型 ===")
		fmt.Println("ChanDir            :", t.ChanDir()) // 通道方向
		fmt.Println("Elem               :", t.Elem())    // 通道元素类型
	// 结构体：
	case reflect.Struct:
		fmt.Println("=== 结构体 ===")
		fmt.Println("NumField           :", t.NumField()) // 字段数量
		if t.NumField() > 0 {
			var i, j int
			// 遍历结构体字段
			for i = 0; i < t.NumField()-1; i++ {
				field := t.Field(i) // 获取结构体字段
				fmt.Printf("    ├ %v\n", field.Name)
				// 遍历嵌套结构体字段
				if field.Type.Kind() == reflect.Struct && field.Type.NumField() > 0 {
					for j = 0; j < field.Type.NumField()-1; j++ {
						subfield := t.FieldByIndex([]int{i, j}) // 获取嵌套结构体字段
						fmt.Printf("    │    ├ %v\n", subfield.Name)
					}
					subfield := t.FieldByIndex([]int{i, j}) // 获取嵌套结构体字段
					fmt.Printf("    │    └ %%v\n", subfield.Name)
				}
			}
			field := t.Field(i) // 获取结构体字段
			fmt.Printf("    └ %v\n", field.Name)
			// 通过名称查找字段
			if field, ok := t.FieldByName("ptr"); ok {
				fmt.Println("FieldByName(ptr)   :", field.Name)
			}
			// 通过函数查找字段
			if field, ok := t.FieldByNameFunc(func(s string) bool { return len(s) > 3 }); ok {
				fmt.Println("FieldByNameFunc    :", field.Name)
			}
		}
	// 函数型：
	case reflect.Func:
		fmt.Println("=== 函数型 ===")
		fmt.Println("IsVariadic         :", t.IsVariadic()) // 是否具有变长参数
		fmt.Println("NumIn              :", t.NumIn())      // 参数数量
		if t.NumIn() > 0 {
			i := 0
			for ; i < t.NumIn()-1; i++ {
				fmt.Println("    ┣", t.In(i)) // 获取参数类型
			}
			fmt.Println("    ┗", t.In(i)) // 获取参数类型
		}
		fmt.Println("NumOut             :", t.NumOut()) // 返回值数量
		if t.NumOut() > 0 {
			i := 0
			for ; i < t.NumOut()-1; i++ {
				fmt.Println("    ┣", t.Out(i)) // 获取返回值类型
			}
			fmt.Println("    ┗", t.Out(i)) // 获取返回值类型
		}
	}
}

------------------------------------------------------------

　　接下来学习 reflect.Value 的所有方法，还是进行分类学习，这里的很多操作（比如取地址、取切片、修改映射、通道进出、取值、赋值、函数调用等）和平时的操作都一样，只不过在这里需要用各种方法来操作，而平时只需要用一些符号来操作。

　　注意：下面描述的 v 值是指 reflect.Value 所代表的实际值，而不是 reflect.Value 本身。

------------------------------

// 特殊

// 判断 v 值是否可寻址
// 1、指针的 Elem() 可寻址
// 2、切片的元素可寻址
// 3、可寻址数组的元素可寻址
// 4、可寻址结构体的字段可寻址，方法不可寻址
// 也就是说，如果 v 值是指向数组的指针“&数组”，通过 v.Elem() 获取该指针指向的数组，那么
// 该数组就是可寻址的，同时该数组的元素也是可寻址的，如果 v 就是一个普通数组，不是通过解引
// 用得到的数组，那么该数组就不可寻址，其元素也不可寻址。结构体亦然。
func (v Value) CanAddr() bool

// 获取 v 值的地址，相当于 & 取地址操作。v 值必须可寻址。
func (v Value) Addr() reflect.Value

// 判断 v 值是否可以被修改。只有可寻址的 v 值可被修改。
// 结构体中的非导出字段（通过 Field() 等方法获取的）不能修改，所有方法不能修改。
func (v Value) CanSet() bool

// 判断 v 值是否可以转换为接口类型
// 结构体中的非导出字段（通过 Field() 等方法获取的）不能转换为接口类型
func (v Value) CanInterface() bool

// 将 v 值转换为空接口类型。v 值必须可转换为接口类型。
func (v Value) Interface() interface{}

// 使用一对 uintptr 返回接口的数据
func (v Value) InterfaceData() [2]uintptr

------------------------------

// 示例：
type ss struct {
	A int
	a int
}

func (s ss) Method1(i int) string  { return "结构体方法1" }
func (s *ss) Method2(i int) string { return "结构体方法2" }

func main() {
	v1 := reflect.ValueOf(ss{})                   // 结构体
	v2 := reflect.ValueOf(&ss{})                  // 结构体指针
	v3 := reflect.ValueOf(&ss{}).Elem()           // 可寻址结构体
	v4 := reflect.ValueOf(&ss{}).Elem().Field(0)  // 可寻址结构体的共有字段
	v5 := reflect.ValueOf(&ss{}).Elem().Field(1)  // 可寻址结构体的私有字段
	v6 := reflect.ValueOf(&ss{}).Method(0)        // 结构体指针的方法
	v7 := reflect.ValueOf(&ss{}).Elem().Method(0) // 结构体的方法

	fmt.Println(v1.CanAddr()) // false
	fmt.Println(v2.CanAddr()) // false
	fmt.Println(v3.CanAddr()) // true
	fmt.Println(v4.CanAddr()) // true
	fmt.Println(v5.CanAddr()) // true
	fmt.Println(v6.CanAddr()) // false
	fmt.Println(v7.CanAddr()) // false
	fmt.Println("----------")
	fmt.Println(v1.CanSet()) // false
	fmt.Println(v2.CanSet()) // false
	fmt.Println(v3.CanSet()) // true
	fmt.Println(v4.CanSet()) // true
	fmt.Println(v5.CanSet()) // false
	fmt.Println(v6.CanSet()) // false
	fmt.Println(v6.CanSet()) // false
	fmt.Println("----------")
	fmt.Println(v1.CanInterface()) // true
	fmt.Println(v2.CanInterface()) // true
	fmt.Println(v3.CanInterface()) // true
	fmt.Println(v4.CanInterface()) // true
	fmt.Println(v5.CanInterface()) // false
	fmt.Println(v6.CanInterface()) // true
	fmt.Println(v7.CanInterface()) // true
}

------------------------------

// 指针

// 将 v 值转换为 uintptr 类型，v 值必须是切片、映射、通道、函数、指针、自由指针。
func (v Value) Pointer() uintptr

// 获取 v 值的地址。v 值必须是可寻址类型（CanAddr）。
func (v Value) UnsafeAddr() uintptr

// 将 UnsafePointer 类别的 v 值修改为 x，v 值必须是 UnsafePointer 类别，必须可修改。
func (v Value) SetPointer(x unsafe.Pointer)

// 判断 v 值是否为 nil，v 值必须是切片、映射、通道、函数、接口、指针。
// IsNil 并不总等价于 Go 的潜在比较规则，比如对于 var i interface{}，i == nil 将返回
// true，但是 reflect.ValueOf(i).IsNil() 将 panic。
func (v Value) IsNil() bool

// 获取“指针所指的对象”或“接口所包含的对象”
func (v Value) Elem() reflect.Value

------------------------------

// 接口

// 获取“指针所指的对象”或“接口所包含的对象”
func (v Value) Elem() reflect.Value

------------------------------

// 通用

// 获取 v 值的字符串描述
func (v Value) String() string

// 获取 v 值的类型
func (v Value) Type() reflect.Type

// 返回 v 值的类别，如果 v 是空值，则返回 reflect.Invalid。
func (v Value) Kind() reflect.Kind

// 获取 v 的方法数量
func (v Value) NumMethod() int

// 根据索引获取 v 值的方法，方法必须存在，否则 panic
// 使用 Call 调用方法的时候不用传入接收者，Go 会自动把 v 作为接收者传入。
func (v Value) Method(int) reflect.Value

// 根据名称获取 v 值的方法，如果该方法不存在，则返回空值（reflect.Invalid）。
func (v Value) MethodByName(string) reflect.Value

// 判断 v 本身（不是 v 值）是否为零值。
// 如果 v 本身是零值，则除了 String 之外的其它所有方法都会 panic。
func (v Value) IsValid() bool

// 将 v 值转换为 t 类型，v 值必须可转换为 t 类型，否则 panic。
func (v Value) Convert(t Type) reflect.Value

------------------------------

// 示例
func main() {
	var v reflect.Value      // 未包含任何数据
	fmt.Println(v.IsValid()) // false

	var i *int
	v = reflect.ValueOf(i)   // 包含一个指针
	fmt.Println(v.IsValid()) // true

	v = reflect.ValueOf(nil) // 包含一个 nil 指针
	fmt.Println(v.IsValid()) // false

	v = reflect.ValueOf(0)   // 包含一个 int 数据
	fmt.Println(v.IsValid()) // true
}

------------------------------

// 获取

// 获取 v 值的内容，如果 v 值不是有符号整型，则 panic。
func (v Value) Int() int64

// 获取 v 值的内容，如果 v 值不是无符号整型（包括 uintptr），则 panic。
func (v Value) Uint() uint64

// 获取 v 值的内容，如果 v 值不是浮点型，则 panic。
func (v Value) Float() float64

// 获取 v 值的内容，如果 v 值不是复数型，则 panic。
func (v Value) Complex() complex128

// 获取 v 值的内容，如果 v 值不是布尔型，则 panic。
func (v Value) Bool() bool

// 获取 v 值的长度，v 值必须是字符串、数组、切片、映射、通道。
func (v Value) Len() int

// 获取 v 值的容量，v 值必须是数值、切片、通道。
func (v Value) Cap() int

// 获取 v 值的第 i 个元素，v 值必须是字符串、数组、切片，i 不能超出范围。
func (v Value) Index(i int) reflect.Value

// 获取 v 值的内容，如果 v 值不是字节切片，则 panic。
func (v Value) Bytes() []byte

// 获取 v 值的切片，切片长度 = j - i，切片容量 = v.Cap() - i。
// v 必须是字符串、数值、切片，如果是数组则必须可寻址。i 不能超出范围。
func (v Value) Slice(i, j int) reflect.Value

// 获取 v 值的切片，切片长度 = j - i，切片容量 = k - i。
// i、j、k 不能超出 v 的容量。i <= j <= k。
// v 必须是字符串、数值、切片，如果是数组则必须可寻址。i 不能超出范围。
func (v Value) Slice3(i, j, k int) reflect.Value

// 根据 key 键获取 v 值的内容，v 值必须是映射。
// 如果指定的元素不存在，或 v 值是未初始化的映射，则返回零值（reflect.ValueOf(nil)）
func (v Value) MapIndex(key Value) reflect.Value

// 获取 v 值的所有键的无序列表，v 值必须是映射。
// 如果 v 值是未初始化的映射，则返回空列表。
func (v Value) MapKeys() []reflect.Value

// 判断 x 是否超出 v 值的取值范围，v 值必须是有符号整型。
func (v Value) OverflowInt(x int64) bool

// 判断 x 是否超出 v 值的取值范围，v 值必须是无符号整型。
func (v Value) OverflowUint(x uint64) bool

// 判断 x 是否超出 v 值的取值范围，v 值必须是浮点型。
func (v Value) OverflowFloat(x float64) bool

// 判断 x 是否超出 v 值的取值范围，v 值必须是复数型。
func (v Value) OverflowComplex(x complex128) bool

------------------------------

// 设置（这些方法要求 v 值必须可修改）

// 设置 v 值的内容，v 值必须是有符号整型。
func (v Value) SetInt(x int64)

// 设置 v 值的内容，v 值必须是无符号整型。
func (v Value) SetUint(x uint64)

// 设置 v 值的内容，v 值必须是浮点型。
func (v Value) SetFloat(x float64)

// 设置 v 值的内容，v 值必须是复数型。
func (v Value) SetComplex(x complex128)

// 设置 v 值的内容，v 值必须是布尔型。
func (v Value) SetBool(x bool)

// 设置 v 值的内容，v 值必须是字符串。
func (v Value) SetString(x string)

// 设置 v 值的长度，v 值必须是切片，n 不能超出范围，不能为负数。
func (v Value) SetLen(n int)

// 设置 v 值的内容，v 值必须是切片，n 不能超出范围，不能小于 Len。
func (v Value) SetCap(n int)

// 设置 v 值的内容，v 值必须是字节切片。x 可以超出 v 值容量。
func (v Value) SetBytes(x []byte)

// 设置 v 值的键和值，如果键存在，则修改其值，如果键不存在，则添加键和值。
// 如果将 val 设置为零值（reflect.ValueOf(nil)），则删除该键。
// 如果 v 值是一个未初始化的 map，则 panic。
func (v Value) SetMapIndex(key, val reflect.Value)

// 设置 v 值的内容，v 值必须可修改，x 必须可以赋值给 v 值。
func (v Value) Set(x reflect.Value)

------------------------------

// 结构体

// 获取 v 值的字段数量，v 值必须是结构体。
func (v Value) NumField() int

// 根据索引获取 v 值的字段，v 值必须是结构体。如果字段不存在则 panic。
func (v Value) Field(i int) reflect.Value

// 根据索引链获取 v 值的嵌套字段，v 值必须是结构体。
func (v Value) FieldByIndex(index []int) reflect.Value

// 根据名称获取 v 值的字段，v 值必须是结构体。
// 如果指定的字段不存在，则返回零值（reflect.ValueOf(nil)）
func (v Value) FieldByName(string) reflect.Value

// 根据匹配函数 match 获取 v 值的字段，v 值必须是结构体。
// 如果没有匹配的字段，则返回零值（reflect.ValueOf(nil)）
func (v Value) FieldByNameFunc(match func(string) bool) Value

------------------------------

// 示例
type ss struct {
	s struct {
		B int
		b int
	}
	A int
	a int
}

func main() {
	var v = reflect.ValueOf(ss{})
	for i := 0; i < v.NumField(); i++ {
		field := v.Field(i)
		fmt.Println("字段：", field.Type().String())
		if field.Type().Kind() == reflect.Struct {
			for j := 0; j < field.NumField(); j++ {
				subfield := field.Field(j)
				fmt.Println("    嵌套字段：", subfield.Type().String())
			}
		}
	}
}

// 输出结果：
// 字段： struct { B int; b int }
//     嵌套字段： int
//     嵌套字段： int
// 字段： int
// 字段： int

------------------------------

// 通道

// 发送数据（会阻塞），v 值必须是可写通道。
func (v Value) Send(x reflect.Value)

// 接收数据（会阻塞），v 值必须是可读通道。
func (v Value) Recv() (x reflect.Value, ok bool)

// 尝试发送数据（不会阻塞），v 值必须是可写通道。
func (v Value) TrySend(x reflect.Value) bool

// 尝试接收数据（不会阻塞），v 值必须是可读通道。
func (v Value) TryRecv() (x reflect.Value, ok bool)

// 关闭通道，v 值必须是通道。
func (v Value) Close()

------------------------------

// 示例
func main() {
	ch := make(chan int, 2)
	v := reflect.ValueOf(ch)

	a := reflect.ValueOf(1)
	b := reflect.ValueOf(2)

	v.Send(a)
	if ok := v.TrySend(b); ok {
		fmt.Println("尝试发送成功！") // 尝试发送成功！
	}
	if i, ok := v.Recv(); ok {
		fmt.Println("接收成功：", i) // 接收成功： 1
	}
	if i, ok := v.TryRecv(); ok {
		fmt.Println("尝试接收成功：", i) // 尝试接收成功： 2
	}
}

------------------------------

// 函数

// 通过参数列表 in 调用 v 值所代表的函数（或方法）。函数的返回值存入 r 中返回。
// 要传入多少参数就在 in 中存入多少元素。
// Call 即可以调用定参函数（参数数量固定），也可以调用变参函数（参数数量可变）。
func (v Value) Call(in []Value) (r []Value)

// 通过参数列表 in 调用 v 值所代表的函数（或方法）。函数的返回值存入 r 中返回。
// 函数指定了多少参数就在 in 中存入多少元素，变参作为一个单独的参数提供。
// CallSlice 只能调用变参函数。
func (v Value) CallSlice(in []Value) []Value

------------------------------

// 示例
var f1 = func(a int, b []int) { fmt.Println(a, b) }
var f2 = func(a int, b ...int) { fmt.Println(a, b) }

func main() {
	v1 := reflect.ValueOf(f1)
	v2 := reflect.ValueOf(f2)

	a := reflect.ValueOf(1)
	b := reflect.ValueOf([]int{1, 2, 3})

	v1.Call([]reflect.Value{a, b})
	v2.Call([]reflect.Value{a, a, a, a, a, a})

	//v1.CallSlice([]reflect.Value{a, b}) // 非变参函数，不能用 CallSlice。
	v2.CallSlice([]reflect.Value{a, b})
}

------------------------------

　　下面的代码用到了所有这些方法：

------------------------------

// 获取各种值的相关信息
package main

import (
	"fmt"
	"reflect"
	"unsafe"
)

// 嵌套结构体
type ss struct {
	a struct {
		int
		string
	}
	int
	string
	bool
	float64
}

func (s ss) Method1(i int) string  { return "结构体方法1" }
func (s *ss) Method2(i int) string { return "结构体方法2" }

var (
	intValue   = int(0)
	int8Value  = int8(8)
	int16Value = int16(16)
	int32Value = int32(32)
	int64Value = int64(64)

	uIntValue   = uint(0)
	uInt8Value  = uint8(8)
	uInt16Value = uint16(16)
	uInt32Value = uint32(32)
	uInt64Value = uint64(64)

	byteValue    = byte(0)
	runeValue    = rune(0)
	uintptrValue = uintptr(0)

	boolValue   = false
	stringValue = "stringValue"

	float32Value = float32(32)
	float64Value = float64(64)

	complex64Value  = complex64(64)
	complex128Value = complex128(128)

	arrayValue  = [5]string{}           // 数组
	sliceValue  = []byte{0, 0, 0, 0, 0} // 切片
	mapValue    = map[string]int{}      // 映射
	chanValue   = make(chan int, 2)     // 通道
	structValue = ss{                   // 结构体
		struct {
			int
			string
		}{10, "子结构体"},
		20,
		"结构体",
		false,
		64.0,
	}

	func1Value = func(i int) string { // 函数（固定参数）
		return fmt.Sprintf("固定参数：%v", i)
	}

	func2Value = func(i ...int) string { // 函数（动态参数）
		return fmt.Sprintf("动态参数：%v", i)
	}

	unsafePointer     = unsafe.Pointer(&structValue)    // 通用指针
	reflectType       = reflect.TypeOf(0)               // 反射类型
	reflectValue      = reflect.ValueOf(0)              // 反射值
	reflectArrayValue = reflect.ValueOf([]int{1, 2, 3}) // 切片反射值
	// 反射接口类型
	interfaceType = reflect.TypeOf(new(interface{})).Elem()
)

// 简单类型
var simpleTypes = []interface{}{
	intValue, &intValue, // int
	int8Value, &int8Value, // int8
	int16Value, &int16Value, // int16
	int32Value, &int32Value, // int32
	int64Value, &int64Value, // int64
	uIntValue, &uIntValue, // uint
	uInt8Value, &uInt8Value, // uint8
	uInt16Value, &uInt16Value, // uint16
	uInt32Value, &uInt32Value, // uint32
	uInt64Value, &uInt64Value, // uint64
	byteValue, &byteValue, // byte
	runeValue, &runeValue, // rune
	uintptrValue, &uintptrValue, // uintptr
	boolValue, &boolValue, // bool
	stringValue, &stringValue, // string
	float32Value, &float32Value, // float32
	float64Value, &float64Value, // float64
	complex64Value, &complex64Value, // complex64
	complex128Value, &complex128Value, // complex128
}

// 复杂类型
var complexTypes = []interface{}{
	arrayValue, &arrayValue, // 数组
	sliceValue, &sliceValue, // 切片
	mapValue, &mapValue, // 映射
	chanValue, &chanValue, // 通道
	structValue, &structValue, // 结构体
	func1Value, &func1Value, // 定参函数
	func2Value, &func2Value, // 动参函数
	structValue.Method1, structValue.Method2, // 方法
	unsafePointer, &unsafePointer, // 指针
	reflectType, &reflectType, // 反射类型
	reflectValue, &reflectValue, // 反射值
	interfaceType, &interfaceType, // 接口反射类型
}

// 空值
var unsafeP unsafe.Pointer

// 空接口
var nilInterfece interface{}

func main() {
	// 测试简单类型
	for i := 0; i < len(simpleTypes); i++ {
		PrintInfo(simpleTypes[i])
	}
	// 测试复杂类型
	for i := 0; i < len(complexTypes); i++ {
		PrintInfo(complexTypes[i])
	}
	// 测试单个对象
	PrintInfo(&unsafeP)
	PrintInfo(nilInterfece)
	// PrintInfo(&nilInterfece) // 会引发 panic
}

func PrintInfo(i interface{}) {
	if i == nil {
		fmt.Println("--------------------")
		fmt.Printf("无效接口值：%v\n", i)
		fmt.Println("--------------------")
		return
	}
	v := reflect.ValueOf(i)
	PrintValue(v)
}

func PrintValue(v reflect.Value) {
	fmt.Println("--------------------")
	// ----- 通用方法 -----
	fmt.Println("String             :", v.String())  // 反射值的字符串形式
	fmt.Println("Type               :", v.Type())    // 反射值的类型
	fmt.Println("Kind               :", v.Kind())    // 反射值的类别
	fmt.Println("CanAddr            :", v.CanAddr()) // 是否可以获取地址
	fmt.Println("CanSet             :", v.CanSet())  // 是否可以修改
	if v.CanAddr() {
		fmt.Println("Addr               :", v.Addr())       // 获取地址
		fmt.Println("UnsafeAddr         :", v.UnsafeAddr()) // 获取自由地址
	}
	// 是否可转换为接口对象
	fmt.Println("CanInterface       :", v.CanInterface())
	if v.CanInterface() {
		fmt.Println("Interface          :", v.Interface()) // 转换为接口对象
	}
	// 获取方法数量
	fmt.Println("NumMethod          :", v.NumMethod())
	if v.NumMethod() > 0 {
		// 遍历方法
		i := 0
		for ; i < v.NumMethod()-1; i++ {
			fmt.Printf("    ┣ %v\n", v.Method(i).String())
			//			if i >= 4 { // 只列举 5 个
			//				fmt.Println("    ┗ ...")
			//				break
			//			}
		}
		fmt.Printf("    ┗ %v\n", v.Method(i).String())
		// 通过名称获取方法
		fmt.Println("MethodByName       :", v.MethodByName("String").String())
	}
	// ----- 可获取指针的类型 -----
	switch v.Kind() {
	case reflect.Slice, reflect.Map, reflect.Chan, reflect.Func,
		reflect.Ptr, reflect.UnsafePointer:
		fmt.Println("Pointer            :", v.Pointer())
	}
	// ----- 特殊类型 -----
	switch v.Kind() {
	// 指针：
	case reflect.Ptr:
		fmt.Println("=== 指针 ===")
		// 获取指针地址
		if !v.IsNil() {
			// 获取指针所指对象
			v = v.Elem() // 只有指针和接口类型可以使用 Elem()
			fmt.Printf("转换到指针所指对象 : %v\n", v.Type())
			// 递归处理指针所指对象
			PrintValue(v)
			return
		}
	// 自由指针：
	case reflect.UnsafePointer:
		fmt.Println("=== 自由指针 ===")
		if v.Pointer() == 0 {
			v.SetPointer(unsafePointer)
			fmt.Println("重新指向新对象     :", v.Pointer())
		}
		// 将自由指针转换为 *ss 指针（因为定义 unsafePointer 时已经确定了类型）
		s := (*ss)(v.Interface().(unsafe.Pointer))
		// 获取反射值
		v = reflect.ValueOf(s)
		if !v.IsNil() {
			// 获取指针所指对象
			v = v.Elem() // 只有指针和接口类型可以使用 Elem()
			fmt.Printf("转换到指针所指对象 : %v\n", v.Type())
			// 递归处理指针所指对象
			PrintValue(v)
			return
		}
	// 接口：
	case reflect.Interface:
		fmt.Println("=== 接口 ===")
		// 获取接口数据
		fmt.Println("InterfaceData      :", v.InterfaceData())
		// 获取接口所包含的对象
		v = v.Elem() // 只有指针和接口类型可以使用 Elem()
		fmt.Printf("转换到接口所含对象 : %v\n", v.Type())
		// 递归处理接口的动态对象
		PrintValue(v)
		return
	}
	// ----- 简单类型 -----
	// 有符号整型：
	if reflect.Int <= v.Kind() && v.Kind() <= reflect.Int64 {
		fmt.Println("=== 有符号整型 ===")
		fmt.Println("Int                :", v.Int()) // 获取值
		if v.CanSet() {
			v.SetInt(10)                                 // 设置值
			fmt.Println("Int                :", v.Int()) // 获取值
			v.Set(reflect.ValueOf(20).Convert(v.Type())) // 设置值
		}
		fmt.Println("Int                :", v.Int())           // 获取值
		fmt.Println("OverflowInt        :", v.OverflowInt(10)) // 是否溢出
	}
	// 无符号整型：
	if reflect.Uint <= v.Kind() && v.Kind() <= reflect.Uint64 {
		fmt.Println("=== 无符号整型 ===")
		fmt.Println("Uint               :", v.Uint()) // 获取值
		if v.CanSet() {
			v.SetUint(10)                                 // 设置值
			fmt.Println("Uint               :", v.Uint()) // 获取值
			v.Set(reflect.ValueOf(20).Convert(v.Type()))  // 设置值
		}
		fmt.Println("Uint               :", v.Uint())           // 获取值
		fmt.Println("OverflowUint       :", v.OverflowUint(10)) // 是否溢出
	}
	switch v.Kind() {
	// 浮点数：
	case reflect.Float32, reflect.Float64:
		fmt.Println("=== 浮点数 ===")
		fmt.Println("Float              :", v.Float()) // 获取值
		if v.CanSet() {
			v.SetFloat(10)                                 // 设置值
			fmt.Println("Float              :", v.Float()) // 获取值
			v.Set(reflect.ValueOf(20).Convert(v.Type()))   // 设置值
		}
		fmt.Println("Float              :", v.Float())           // 获取值
		fmt.Println("OverflowFloat      :", v.OverflowFloat(10)) // 是否溢出
	// 复数：
	case reflect.Complex64, reflect.Complex128:
		fmt.Println("=== 复数 ===")
		fmt.Println("Complex            :", v.Complex()) // 获取值
		if v.CanSet() {
			v.SetComplex(10)                                   // 设置值
			fmt.Println("Complex            :", v.Complex())   // 获取值
			v.Set(reflect.ValueOf(20 + 20i).Convert(v.Type())) // 设置值
		}
		fmt.Println("Complex            :", v.Complex())           // 获取值
		fmt.Println("OverflowComplex    :", v.OverflowComplex(10)) // 是否溢出
	// 布尔型：
	case reflect.Bool:
		fmt.Println("=== 布尔型 ===")
		fmt.Println("Bool               :", v.Bool()) // 获取值
		if v.CanSet() {
			v.SetBool(true)                               // 设置值
			fmt.Println("Bool               :", v.Bool()) // 获取值
			v.Set(reflect.ValueOf(false))                 // 设置值
		}
		fmt.Println("Bool               :", v.Bool()) // 获取值
	// 字符串：
	case reflect.String:
		fmt.Println("=== 字符串 ===")
		fmt.Println("String             :", v.String()) // 获取值
		if v.CanSet() {
			v.SetString("abc")                              // 设置值
			fmt.Println("String             :", v.String()) // 获取值
			v.Set(reflect.ValueOf("def"))                   // 设置值
		}
		fmt.Println("String             :", v.String()) // 获取值
	// ----- 复杂类型 -----
	// 切片型：
	case reflect.Slice:
		fmt.Println("=== 切片型 ===")
		fmt.Println("Len                :", v.Len()) // 获取长度
		fmt.Println("Cap                :", v.Cap()) // 获取容量
		if v.CanSet() {
			v.SetLen(4) // 不能大于 cap
			v.SetCap(4) // 不能小于 len，只能缩，不能扩
			fmt.Println("SetLen, SetCap     :", v.Len(), v.Cap())
			// 重新指定字节内容
			if v.Type().Elem().Kind() == reflect.Uint8 {
				v.SetBytes([]byte{1, 2, 3, 4, 5, 6, 7, 8, 9, 0})
			}
			fmt.Println("SetByte            :", []byte{1, 2, 3, 4, 5, 6, 7, 8, 9, 0})
		}
		// 获取字节内容
		if v.Type().Elem().Kind() == reflect.Uint8 {
			fmt.Println("Bytes              :", v.Bytes())
		}
		// 根据索引获取元素
		if v.Len() > 0 {
			for i := 0; i < v.Len(); i++ {
				fmt.Println("Index              :", v.Index(i))
			}
		}
		// 获取一个指定范围的切片
		// 参数：起始下标，结束下标
		// 长度 = 结束下标 - 起始下标
		s1 := v.Slice(1, 2)
		fmt.Println("Slice              :", s1)
		fmt.Println("Len                :", s1.Len()) // 获取长度
		fmt.Println("Cap                :", s1.Cap()) // 获取容量
		// 获取一个指定范围和容量的切片
		// 参数：起始下标，结束下标，容量下标
		// 长度 = 结束下标 - 起始下标
		// 容量 = 容量下标 - 起始下标
		s2 := v.Slice3(1, 2, 4)
		fmt.Println("Slice              :", s2)
		fmt.Println("Len                :", s2.Len()) // 获取长度
		fmt.Println("Cap                :", s2.Cap()) // 获取容量
	// 映射型：
	case reflect.Map:
		fmt.Println("=== 映射型 ===")
		// 设置键值，不需要检测 CanSet
		v.SetMapIndex(reflect.ValueOf("a"), reflect.ValueOf(1))
		v.SetMapIndex(reflect.ValueOf("b"), reflect.ValueOf(2))
		v.SetMapIndex(reflect.ValueOf("c"), reflect.ValueOf(3))
		// 获取键列表
		fmt.Println("MapKeys            :", v.MapKeys())
		for _, idx := range v.MapKeys() {
			// 根据键获取值
			fmt.Println("MapIndex           :", v.MapIndex(idx))
		}
	// 结构体：
	case reflect.Struct:
		fmt.Println("=== 结构体 ===")
		// 获取字段个数
		fmt.Println("NumField           :", v.NumField())
		if v.NumField() > 0 {
			var i, j int
			// 遍历结构体字段
			for i = 0; i < v.NumField()-1; i++ {
				field := v.Field(i) // 获取结构体字段
				fmt.Printf("    ├ %-8v %v\n", field.Type(), field.String())
				// 遍历嵌套结构体字段
				if field.Kind() == reflect.Struct && field.NumField() > 0 {
					for j = 0; j < field.NumField()-1; j++ {
						subfield := v.FieldByIndex([]int{i, j}) // 获取嵌套结构体字段
						fmt.Printf("    │    ├ %-8v %v\n", subfield.Type(), subfield.String())
						// if i >= 4 { // 只列举 5 个
						// 	fmt.Println("        ┗ ...")
						// 	break
						// }
					}
					subfield := v.FieldByIndex([]int{i, j}) // 获取嵌套结构体字段
					fmt.Printf("    │    └ %-8v %v\n", subfield.Type(), subfield.String())
				}
				// if i >= 4 { // 只列举 5 个
				// 	fmt.Println("    ┗ ...")
				// 	break
				// }
			}
			field := v.Field(i) // 获取结构体字段
			fmt.Printf("    └ %-8v %v\n", field.Type(), field.String())
			// 通过名称查找字段
			if v := v.FieldByName("ptr"); v.IsValid() {
				fmt.Println("FieldByName(ptr)   :", v.Type().Name())
			}
			// 通过函数查找字段
			v := v.FieldByNameFunc(func(s string) bool { return len(s) > 3 })
			if v.IsValid() {
				fmt.Println("FieldByNameFunc    :", v.Type().Name())
			}
		}
	// 通道型：
	case reflect.Chan:
		fmt.Println("=== 通道型 ===")
		// 发送数据（会阻塞）
		v.Send(reflectValue)
		// 尝试发送数据（不会阻塞）
		fmt.Println("TrySend            :", v.TrySend(reflectValue))
		// 接收数据（会阻塞）
		if x, ok := v.Recv(); ok {
			fmt.Println("Recv               :", x) //
		}
		// 尝试接收数据（不会阻塞）
		if x, ok := v.TryRecv(); ok {
			fmt.Println("TryRecv            :", x) //
		}
		// 因为要执行两次，通道和通道指针各执行一次，关闭后第二次就无法执行了。
		// v.Close()
	// 函数型：
	case reflect.Func:
		fmt.Println("=== 函数型 ===")
		// 判断函数是否具有变长参数
		if v.Type().IsVariadic() {
			// 与可变参数对应的实参必须是切片类型的反射值（reflectArrayValue）。
			fmt.Println("CallSlice          :", v.CallSlice([]reflect.Value{reflectArrayValue})) //
			// 也可以用 v.Call 调用变长参数的函数，只需传入 reflectValue 即可。
		} else {
			// 根据函数定义的参数数量，传入相应数量的反射值（reflectValue）。
			fmt.Println("Call               :", v.Call([]reflect.Value{reflectValue})) //
		}
	}
}
```
------------------------------------------------------------