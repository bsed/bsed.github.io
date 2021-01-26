---
title: "Golang学习 - print.go"
categories: [ "Golang" ]
tags: [ "golang","print" ]
draft: false
slug: "golang-learning-printgo"
date: "2016-06-06 16:30:00"
---

```golang
// Copyright 2009 The Go Authors. All rights reserved.
// Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

// go/src/fmt/print.go
// version 1.7

package fmt

import (
	"errors"
	"io"
	"os"
	"reflect"
	"sync"
	"unicode/utf8"
)


<!--more-->


// 用于 buffer.WriteString 的字符串，比使用 buffer.Write 写入字节数组更节省开销。
const (
	commaSpaceString  = ", "
	nilAngleString    = "<nil>"
	nilParenString    = "(nil)"
	nilString         = "nil"
	mapString         = "map["
	percentBangString = "%!"
	missingString     = "(MISSING)"
	badIndexString    = "(BADINDEX)"
	panicString       = "(PANIC="
	extraString       = "%!(EXTRA "
	badWidthString    = "%!(BADWIDTH)"
	badPrecString     = "%!(BADPREC)"
	noVerbString      = "%!(NOVERB)"
	invReflectString  = "<invalid reflect.Value>"
)

// State 提供格式化器的相关信息，传递给 Formatter 使用
type State interface {
	// 将格式化后的数据写入缓冲区中准备打印
	Write(b []byte) (n int, err error)
	// 返回宽度信息及其是否被设置
	Width() (wid int, ok bool)
	// 返回精度信息及其是否被设置
	Precision() (prec int, ok bool)
	// 返回旗标 [+- 0#] 是否被设置
	Flag(c int) bool
}

// Formatter 用于让自定义类型实现自己的格式化过程。
// Format 方法会被格式化器调用，只要对应的 arg 实现了该方法。
// f 是格式化器的当前状态，c 是占位符中指定的动词。
// 可以通过 f.Width 和 f.Precision 获取占位符中的宽度和精度信息
// 可以通过 f.Flag(c) 判断占位符中是否设置了某个旗标（+- 0#）
// 格式化后的结果通过 f.Write 写回去。
type Formatter interface {
	Format(f State, c rune)
}

// 格式化器在格式化某值的本地字符串时，会调用其类型的 String 方法。
type Stringer interface {
	String() string
}

// 格式化器在格式化某值的 Go 语法字符串（%#v）时，会调用其类型的 GoString 方法。
type GoStringer interface {
	GoString() string
}

// 使用简单的 []byte 代替 bytes.Buffer 避免大的依赖。
type buffer []byte

func (b *buffer) Write(p []byte) {
	*b = append(*b, p...)
}

func (b *buffer) WriteString(s string) {
	*b = append(*b, s...)
}

func (b *buffer) WriteByte(c byte) {
	*b = append(*b, c)
}

func (bp *buffer) WriteRune(r rune) {
	// 单字节 UTF8 字符快速处理
	if r < utf8.RuneSelf {
		*bp = append(*bp, byte(r))
		return
	}
	b := *bp
	// 准备缓冲区空间，用于写入 rune 编码
	n := len(b)
	for n+utf8.UTFMax > cap(b) {
		b = append(b, 0)
	}
	// 写入 rune 字符（必须提供足够的空间）
	w := utf8.EncodeRune(b[n:n+utf8.UTFMax], r)
	// 截断无效部分
	*bp = b[:n+w]
}

// pp 是格式化器，整个格式化过程都是由它完成的。
// 格式化结果将写入缓冲区 buf 中。
type pp struct {
	// 存放结果的缓冲区 []byte
	buf buffer

	// 当前需要格式化的参数
	arg interface{}

	// 用于代替 arg，有反射值的时候就不用 arg 了
	value reflect.Value

	// fmt 用于格式化基础类型
	// fmt 在 format.go 中定义
	fmt fmt

	// 是否使用了自定义的 argNum（argNum 是正在处理的 arg 的序号）。
	reordered bool
	// 当前 argNum 是否合法（比如自定义 argNum 格式错误，或 argNum 超出范围）。
	goodArgNum bool
	// 用在 catchPanic 方法中，避免无限的“恐慌->恢复->恐慌->恢复...”递归情况。
	panicking bool
	// 当打印一个错误“动词”时设置 erroring，以防止调用 handleMethods。
	// 错误的“动词”不能用自定义方法去处理。
	erroring bool
}

// 临时对象池
var ppFree = sync.Pool{
	New: func() interface{} { return new(pp) },
}

// 分配一个新的格式化器，或从临时对象池中取出一个。
func newPrinter() *pp {
	p := ppFree.Get().(*pp)
	p.panicking = false
	p.erroring = false
	p.fmt.init(&p.buf)
	return p
}

// 将用过的格式化器存入临时对象池中，避免每次调用时都重新分配内存。
func (p *pp) free() {
	p.buf = p.buf[:0]
	p.arg = nil
	p.value = reflect.Value{}
	ppFree.Put(p)
}

// 实现 State 接口
func (p *pp) Width() (wid int, ok bool) { return p.fmt.wid, p.fmt.widPresent }

// 实现 State 接口
func (p *pp) Precision() (prec int, ok bool) { return p.fmt.prec, p.fmt.precPresent }

// 实现 State 接口
func (p *pp) Flag(b int) bool {
	switch b {
	case '-':
		return p.fmt.minus
	case '+':
		return p.fmt.plus || p.fmt.plusV
	case '#':
		return p.fmt.sharp || p.fmt.sharpV
	case ' ':
		return p.fmt.space
	case '0':
		return p.fmt.zero
	}
	return false
}

// 实现 State 接口
func (p *pp) Write(b []byte) (ret int, err error) {
	p.buf.Write(b)
	return len(b), nil
}

// 这些函数以 f 结尾，并需要一个格式字符串。

// Fprintf 根据格式字符串 format 对 a 中提供的 arg 进行格式化，
// 并将结果写入 w。返回写入的字节数和错误信息。
func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrintf(format, a)
	n, err = w.Write(p.buf)
	p.free()
	return
}

// Printf 根据格式字符串 format 对 a 中提供的 arg 进行格式化，
// 并将结果写入标准输出。返回写入的字节数和错误信息。
func Printf(format string, a ...interface{}) (n int, err error) {
	return Fprintf(os.Stdout, format, a...)
}

// Sprintf 根据格式字符串 format 对 a 中提供的 arg 进行格式化，并返回结果。
func Sprintf(format string, a ...interface{}) string {
	p := newPrinter()
	p.doPrintf(format, a)
	s := string(p.buf)
	p.free()
	return s
}

// Errorf 根据格式字符串 format 对 a 中提供的 arg 进行格式化，
// 并将结果包装成 error 类型返回。
func Errorf(format string, a ...interface{}) error {
	return errors.New(Sprintf(format, a...))
}

// 这些函数没有格式字符串

// Fprint 使用默认格式对 a 中提供的 arg 进行格式化，并将结果写入 w。
// 返回写入的字节数和错误信息。
// 非字符串 arg 之间会添加空格。
func Fprint(w io.Writer, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrint(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}

// Print 使用默认格式对 a 中提供的 arg 进行格式化，并将结果写入到标准输出。
// 返回写入的字节数和错误信息。
// 非字符串 arg 之间会添加空格。
func Print(a ...interface{}) (n int, err error) {
	return Fprint(os.Stdout, a...)
}

// Sprint 使用默认格式对 a 中提供的 arg 进行格式化，并返回结果。
// 非字符串 arg 之间会添加空格。
func Sprint(a ...interface{}) string {
	p := newPrinter()
	p.doPrint(a)
	s := string(p.buf)
	p.free()
	return s
}

// 这些函数以 ln 结尾，不需要格式字符串。

// Fprintln 使用默认格式对 a 中提供的 arg 进行格式化，并将结果写入 w。
// 返回写入的字节数和错误信息。
// 各 arg 之间会添加空格，并在最后添加换行符。
func Fprintln(w io.Writer, a ...interface{}) (n int, err error) {
	p := newPrinter()
	p.doPrintln(a)
	n, err = w.Write(p.buf)
	p.free()
	return
}

// Println 使用默认格式对 a 中提供的 arg 进行格式化，并将结果写入标准输出。
// 返回写入的字节数和错误信息。
// 各 arg 之间会添加空格，并在最后添加换行符。
func Println(a ...interface{}) (n int, err error) {
	return Fprintln(os.Stdout, a...)
}

// Sprintln 使用默认格式对 a 中提供的 arg 进行格式化，并返回结果。
// 各 arg 之间会添加空格，并在最后添加换行符。
func Sprintln(a ...interface{}) string {
	p := newPrinter()
	p.doPrintln(a)
	s := string(p.buf)
	p.free()
	return s
}

// 获取结构体的第 i 个字段。如果字段自身是接口类型，
// 则返回接口中的值，而不是接口自身。
func getField(v reflect.Value, i int) reflect.Value {
	val := v.Field(i)
	if val.Kind() == reflect.Interface && !val.IsNil() {
		val = val.Elem()
	}
	return val
}

// 判断整数值是否过大，超出格式化宽度和精度的允许范围。
func tooLarge(x int) bool {
	const max int = 1e6
	return x > max || x < -max
}

// 将字符串格式的数值尽可能解析为 int 型数值，直到遇到非数字字符为止。
// 如果没有解析出任何数值，则 num 返回 0，且 isnum 返回 false。
// newi 返回待处理下标（即：处理完开头的所有数字之后的下一个字符的下标）
func parsenum(s string, start, end int) (num int, isnum bool, newi int) {
	if start >= end {
		return 0, false, end
	}
	// 在“数字字符串”范围内循环，并计算结果
	for newi = start; newi < end && '0' <= s[newi] && s[newi] <= '9'; newi++ {
		if tooLarge(num) {
			return 0, false, end
		}
		num = num*10 + int(s[newi]-'0')
		isnum = true
	}
	return
}

// 写入未知类型的值
func (p *pp) unknownType(v reflect.Value) {
	if !v.IsValid() {
		p.buf.WriteString(nilAngleString)
		return
	}
	p.buf.WriteByte('?')
	p.buf.WriteString(v.Type().String())
	p.buf.WriteByte('?')
}

// 处理无效动词（无法被指定类型识别的动词）。比如布尔型只能
// 识别 v 和 t 两个动词，那么其它动词对布尔型来说就是无效动词。
func (p *pp) badVerb(verb rune) {
	// 标记 erroring，防止调用 handleMethods 方法，
	// 因为无效动词不能交给类型自己去处理。
	p.erroring = true
	p.buf.WriteString(percentBangString)  // "%!"
	p.buf.WriteRune(verb)
	p.buf.WriteByte('(')
	switch {
	case p.arg != nil:
		p.buf.WriteString(reflect.TypeOf(p.arg).String())
		p.buf.WriteByte('=')
		p.printArg(p.arg, 'v')
	case p.value.IsValid():               // (*int)(nil)
		p.buf.WriteString(p.value.Type().String())
		p.buf.WriteByte('=')
		p.printValue(p.value, 'v', 0)
	default:
		p.buf.WriteString(nilAngleString) // "<nil>"
	}
	p.buf.WriteByte(')')
	p.erroring = false
}

// 写入布尔型值
func (p *pp) fmtBool(v bool, verb rune) {
	switch verb {
	case 't', 'v':
		p.fmt.fmt_boolean(v)
	default:
		p.badVerb(verb)
	}
}

// 写入整型值的十六进制格式
// leading0x：是否添加 0x 前导符
func (p *pp) fmt0x64(v uint64, leading0x bool) {
	// 通过临时设置 # 旗标来添加 0x 前导符
	sharp := p.fmt.sharp
	p.fmt.sharp = leading0x
	p.fmt.fmt_integer(v, 16, unsigned, ldigits)
	p.fmt.sharp = sharp
}

// 写入整型值，包有符号和无符号
func (p *pp) fmtInteger(v uint64, isSigned bool, verb rune) {
	switch verb {
	case 'v': // 十进制
		if p.fmt.sharpV && !isSigned { // Go 语法格式：无符号型用 0xFF 格式
			p.fmt0x64(v, true)
		} else {
			p.fmt.fmt_integer(v, 10, isSigned, ldigits)
		}
	case 'd': // 十进制
		p.fmt.fmt_integer(v, 10, isSigned, ldigits)
	case 'b': // 二进制
		p.fmt.fmt_integer(v, 2, isSigned, ldigits)
	case 'o': // 八进制
		p.fmt.fmt_integer(v, 8, isSigned, ldigits)
	case 'x': // 十六进制（小写）
		p.fmt.fmt_integer(v, 16, isSigned, ldigits)
	case 'X': // 十六进制（大写）
		p.fmt.fmt_integer(v, 16, isSigned, udigits)
	case 'c': // 字符
		p.fmt.fmt_c(v)
	case 'q': // '字符'
		if v <= utf8.MaxRune {
			p.fmt.fmt_qc(v)
		} else {
			p.badVerb(verb)
		}
	case 'U': // U+FFFF
		p.fmt.fmt_unicode(v)
	default:
		p.badVerb(verb)
	}
}

// 写入浮点型值
func (p *pp) fmtFloat(v float64, size int, verb rune) {
	// fmt_float 的最后一个参数指定精度
	switch verb {
	case 'v':
		p.fmt.fmt_float(v, size, 'g', -1)
	case 'b', 'g', 'G':
		p.fmt.fmt_float(v, size, verb, -1)
	case 'f', 'e', 'E':
		p.fmt.fmt_float(v, size, verb, 6)
	case 'F':
		p.fmt.fmt_float(v, size, 'f', 6)
	default:
		p.badVerb(verb)
	}
}

// 写入复数型值
// 将实部和虚部用 fmtFloat 分别格式化后写入缓冲区。
func (p *pp) fmtComplex(v complex128, size int, verb rune) {
	switch verb {
	case 'v', 'b', 'g', 'G', 'f', 'F', 'e', 'E':
		oldPlus := p.fmt.plus
		p.buf.WriteByte('(')
		p.fmtFloat(real(v), size/2, verb)
		// 虚部总有一个符号
		p.fmt.plus = true
		p.fmtFloat(imag(v), size/2, verb)
		p.buf.WriteString("i)")
		p.fmt.plus = oldPlus
	default:
		p.badVerb(verb)
	}
}

// 写入字符串
func (p *pp) fmtString(v string, verb rune) {
	switch verb {
	case 'v': // 字符串（无引号）
		if p.fmt.sharpV { // Go 语法格式：字符串（有引号）
			p.fmt.fmt_q(v)
		} else {
			p.fmt.fmt_s(v)
		}
	case 's': // 字符串（无引号）
		p.fmt.fmt_s(v)
	case 'x': // 十六进制字符串（小写）
		p.fmt.fmt_sx(v, ldigits)
	case 'X': // 十六进制字符串（大写）
		p.fmt.fmt_sx(v, udigits)
	case 'q': // 字符串（有引号）
		p.fmt.fmt_q(v)
	default:
		p.badVerb(verb)
	}
}

// 写入字节切片型
func (p *pp) fmtBytes(v []byte, verb rune, typeString string) {
	switch verb {
	case 'v', 'd': // [元素 元素 ...]
		if p.fmt.sharpV { // Go 语法格式：[]byte{0xFF, 0xFF, ...}
			p.buf.WriteString(typeString)
			if v == nil {
				p.buf.WriteString(nilParenString)       // "(nil)"
				return
			}
			p.buf.WriteByte('{')
			for i, c := range v {
				if i > 0 { // 第一个元素前面不添加分隔符
					p.buf.WriteString(commaSpaceString) // ", "
				}
				p.fmt0x64(uint64(c), true)
			}
			p.buf.WriteByte('}')
		} else {
			p.buf.WriteByte('[')
			for i, c := range v {
				if i > 0 { // 第一个元素前面不添加分隔符
					p.buf.WriteByte(' ')
				}
				p.fmt.fmt_integer(uint64(c), 10, unsigned, ldigits)
			}
			p.buf.WriteByte(']')
		}
	case 's': // 字符串（无引号）
		p.fmt.fmt_s(string(v))
	case 'x': // 十六进制字符串（小写）
		p.fmt.fmt_bx(v, ldigits)
	case 'X': // 十六进制字符串（大写）
		p.fmt.fmt_bx(v, udigits)
	case 'q': // 字符串（有引号）
		p.fmt.fmt_q(string(v))
	default:
		p.printValue(reflect.ValueOf(v), verb, 0)
	}
}

// 写入指针
func (p *pp) fmtPointer(value reflect.Value, verb rune) {
	var u uintptr
	// 只接受指定类型的值并转换为指针：通道、函数、映射、指针、切片、非安全指针
	switch value.Kind() {
	case reflect.Chan, reflect.Func, reflect.Map, reflect.Ptr, reflect.Slice, reflect.UnsafePointer:
		u = value.Pointer()
	default:
		p.badVerb(verb)
		return
	}

	switch verb {
	case 'v': // 0xffffffffff（或 "nil"）
		if p.fmt.sharpV { // Go 语法格式：(类型)(0xffffffffff)
			p.buf.WriteByte('(')
			p.buf.WriteString(value.Type().String())
			p.buf.WriteString(")(")
			if u == 0 {
				p.buf.WriteString(nilString)    // "nil"
			} else {
				p.fmt0x64(uint64(u), true)
			}
			p.buf.WriteByte(')')
		} else {
			if u == 0 {
				p.fmt.padString(nilAngleString) // <"nil">
			} else {
				p.fmt0x64(uint64(u), !p.fmt.sharp)
			}
		}
	case 'p': // 0xffffffffff
		p.fmt0x64(uint64(u), !p.fmt.sharp)
	case 'b', 'o', 'd', 'x', 'X': // 当整数处理
		p.fmtInteger(uint64(u), unsigned, verb)
	default:
		p.badVerb(verb)
	}
}

// 用在 handleMethods 方法中，捕捉自定义类型的格式化方法中产生的恐慌。
func (p *pp) catchPanic(arg interface{}, verb rune) {
	if err := recover(); err != nil {
		// 如果 arg 是一个 nil，只需要写入 "<nil>" 即可。出现这种 panic 最有
		// 可能的原因是 Stringer 未能防止无效的 nil 或以 nil 做为接收器。无论
		// 哪种情况，写入 "<nil>" 是一个很好的选择。
		if v := reflect.ValueOf(arg); v.Kind() == reflect.Ptr && v.IsNil() {
			p.buf.WriteString(nilAngleString)
			return
		}
		// 否则通过后面的一系列语句打印一个简明的 panic 信息即可。
		if p.panicking {
			// 如果后面的 printArg 中又产生了 panic，那么真的就恐慌了。
			panic(err)
		}
		p.fmt.clearflags() // 一个占位符算处理完了，进行复位。
		p.buf.WriteString(percentBangString)
		p.buf.WriteRune(verb)
		p.buf.WriteString(panicString)
		p.panicking = true // 防止“恐慌 -> 恢复 -> 恐慌 -> 恢复”无限递归
		p.printArg(err, 'v')
		p.panicking = false
		p.buf.WriteByte(')')
	}
}

// 判断 p.arg 是否有自定义的格式化方法，如果有就调用，如果没有，就返回 false。
func (p *pp) handleMethods(verb rune) (handled bool) {
	// 如果正在处理无效动词，则不使用自定义方法。
	if p.erroring {
		return
	}
	// 实现了 Format 方法
	if formatter, ok := p.arg.(Formatter); ok {
		// 标记 p.arg 已经处理了。
		handled = true
		// 捕捉 Format() 中产生的恐慌
		defer p.catchPanic(p.arg, verb)
		// 处理 arg
		formatter.Format(p, verb)
		return
	}
	// 要求 Go 语法格式
	if p.fmt.sharpV {
		// 实现了 GoString 方法
		if stringer, ok := p.arg.(GoStringer); ok {
			handled = true
			// 捕捉 GoString() 中产生的恐慌
			defer p.catchPanic(p.arg, verb)
			// 处理 arg
			p.fmt.fmt_s(stringer.GoString())
			return
		}
	} else {
		// 如果指定了字符串相关的动词，则判断 p.arg 可否转换为字符串
		switch verb {
		case 'v', 's', 'x', 'X', 'q':
			switch v := p.arg.(type) {
			case error:    // Error() 方法可以获取到字符串
				handled = true
				// 捕捉 Error() 中产生的恐慌
				defer p.catchPanic(p.arg, verb)
				// 处理 arg
				p.fmtString(v.Error(), verb)
				return

			case Stringer: // String() 方法可以获取到字符串
				handled = true
				// 捕捉 String() 中产生的恐慌
				defer p.catchPanic(p.arg, verb)
				// 处理 arg
				p.fmtString(v.String(), verb)
				return
			}
		}
	}
	return false
}

// printArg 只处理非嵌套的基础类型，其它类型的 arg，则交给 printValue 进行
// 反射处理。如果 arg 有自定义的格式化方法，则 printArg 会调用自定义方法进行处理。
// 对于嵌套中的类型，也是交给 printValue 进行处理。
func (p *pp) printArg(arg interface{}, verb rune) {
	// 记下当前 arg 内容，方便其它地方使用
	p.arg = arg
	// 初始化为零值，防止其它地方调用了 nil
	p.value = reflect.Value{}

	if arg == nil {
		switch verb {
		case 'T', 'v':
			p.fmt.padString(nilAngleString) // "<nil>"
		default:
			p.badVerb(verb)
		}
		return
	}

	// %T（类型）和 %p（指针）是特殊动词，需要优先处理。
	switch verb {
	case 'T':
		p.fmt.fmt_s(reflect.TypeOf(arg).String())
		return
	case 'p':
		p.fmtPointer(reflect.ValueOf(arg), 'p')
		return
	}

	// 简单类型可以不用反射处理，直接调用相应的处理函数即可。
	switch f := arg.(type) {
	case bool:
		p.fmtBool(f, verb)
	case float32:
		p.fmtFloat(float64(f), 32, verb)
	case float64:
		p.fmtFloat(f, 64, verb)
	case complex64:
		p.fmtComplex(complex128(f), 64, verb)
	case complex128:
		p.fmtComplex(f, 128, verb)
	case int:
		p.fmtInteger(uint64(f), signed, verb)
	case int8:
		p.fmtInteger(uint64(f), signed, verb)
	case int16:
		p.fmtInteger(uint64(f), signed, verb)
	case int32:
		p.fmtInteger(uint64(f), signed, verb)
	case int64:
		p.fmtInteger(uint64(f), signed, verb)
	case uint:
		p.fmtInteger(uint64(f), unsigned, verb)
	case uint8:
		p.fmtInteger(uint64(f), unsigned, verb)
	case uint16:
		p.fmtInteger(uint64(f), unsigned, verb)
	case uint32:
		p.fmtInteger(uint64(f), unsigned, verb)
	case uint64:
		p.fmtInteger(f, unsigned, verb)
	case uintptr:
		p.fmtInteger(uint64(f), unsigned, verb)
	case string:
		p.fmtString(f, verb)
	case []byte:
		p.fmtBytes(f, verb, "[]byte")
	case reflect.Value:
		p.printValue(f, verb, 0)
	default:
		// 其它类型可能有自定义的格式化方法，在这里调用。
		if !p.handleMethods(verb) {
			// 如果该类型没有可用于格式化的方法，则通过反射处理。
			p.printValue(reflect.ValueOf(f), verb, 0)
		}
	}
}

var byteType = reflect.TypeOf(byte(0))

// printValue 类似于 printArg 但是以反射值作为参数，而不是接口值。
// 它不处理 'p' 和 'T' 动词，因为这些已经被 printArg 处理过了。
// depth 表示嵌套深度，比如结构体中嵌套的类型，其深度就大于 0。
func (p *pp) printValue(value reflect.Value, verb rune, depth int) {
	// 嵌套中的值，即使有自定义的格式化方法，也不会被 printArg 调用，在这里进行调用
	if depth > 0 && value.IsValid() && value.CanInterface() {
		p.arg = value.Interface()
		if p.handleMethods(verb) {
			return
		}
	}
	p.arg = nil     // 传入的是反射值，反射值不一定是通过 arg 获取到的，清空防干扰
	p.value = value // 记下 value 内容，方便其它地方使用

	switch f := value; value.Kind() {
	case reflect.Invalid:
		if depth == 0 { // 非嵌套的无效值
			p.buf.WriteString(invReflectString)   // "<invalid reflect.Value>"
		} else {        // 嵌套的无效值
			switch verb {
			case 'v':
				p.buf.WriteString(nilAngleString) // "<nil>"
			default:
				p.badVerb(verb)
			}
		}
	// 不同类型的处理方法和 printArg 中相同。
	case reflect.Bool:
		p.fmtBool(f.Bool(), verb)
	case reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64:
		p.fmtInteger(uint64(f.Int()), signed, verb)
	case reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64, reflect.Uintptr:
		p.fmtInteger(f.Uint(), unsigned, verb)
	case reflect.Float32:
		p.fmtFloat(f.Float(), 32, verb)
	case reflect.Float64:
		p.fmtFloat(f.Float(), 64, verb)
	case reflect.Complex64:
		p.fmtComplex(f.Complex(), 64, verb)
	case reflect.Complex128:
		p.fmtComplex(f.Complex(), 128, verb)
	case reflect.String:
		p.fmtString(f.String(), verb)
	// 嵌套类型 map，需要递归
	case reflect.Map:
		if p.fmt.sharpV { // Go 语法格式
			p.buf.WriteString(f.Type().String())
			if f.IsNil() {
				p.buf.WriteString(nilParenString)        // "(nil)"
				return
			}
			p.buf.WriteByte('{')
		} else {
			p.buf.WriteString(mapString)                 // "map["
		}
		keys := f.MapKeys()
		for i, key := range keys {
			if i > 0 { // 第一个元素前面不添加分隔符
				if p.fmt.sharpV { // Go 语法格式
					p.buf.WriteString(commaSpaceString)  // ", "
				} else {
					p.buf.WriteByte(' ')
				}
			}
			p.printValue(key, verb, depth+1)             // 递归处理键
			p.buf.WriteByte(':')
			p.printValue(f.MapIndex(key), verb, depth+1) // 递归处理值
		}
		if p.fmt.sharpV { // Go 语法格式
			p.buf.WriteByte('}')
		} else {
			p.buf.WriteByte(']')
		}
	// 嵌套类型 struct，需要递归
	case reflect.Struct:
		if p.fmt.sharpV { // Go 语法格式
			p.buf.WriteString(f.Type().String())
		}
		p.buf.WriteByte('{')
		for i := 0; i < f.NumField(); i++ {
			if i > 0 { // 第一个元素前面不添加分隔符
				if p.fmt.sharpV { // Go 语法格式
					p.buf.WriteString(commaSpaceString)  // ", "
				} else {
					p.buf.WriteByte(' ')
				}
			}
			// 结构体字段语法格式 || Go 语法格式（类型:值）
			if p.fmt.plusV || p.fmt.sharpV {
				if name := f.Type().Field(i).Name; name != "" {
					p.buf.WriteString(name)
					p.buf.WriteByte(':')
				}
			}
			// 递归处理成员
			p.printValue(getField(f, i), verb, depth+1)
		}
		p.buf.WriteByte('}')
	// 接口（包含实现该接口的对象），需要递归
	case reflect.Interface:
		value := f.Elem() // 获取接口中的对象
		if !value.IsValid() {
			if p.fmt.sharpV { // Go 语法格式
				p.buf.WriteString(f.Type().String())
				p.buf.WriteString(nilParenString) // "(nil)"
			} else {
				p.buf.WriteString(nilAngleString) // "<nil>"
			}
		} else {
			// 递归处理接口中的对象
			p.printValue(value, verb, depth+1)
		}
	// 嵌套类型 map、slice，可能需要递归（如果元素不是字节的话）
	case reflect.Array, reflect.Slice:
		switch verb {
		case 's', 'q', 'x', 'X':
			// 处理字节类型的切片和数组，它们对于上面的动词而言比较特殊，不用递归。
			t := f.Type()
			if t.Elem().Kind() == reflect.Uint8 { // 判断元素类型
				var bytes []byte
				if f.Kind() == reflect.Slice {    // 切片类型（直接获取字节内容）
					bytes = f.Bytes()
				} else if f.CanAddr() {           // 可以转换成切片
					bytes = f.Slice(0, f.Len()).Bytes()
				} else {                          // 不能转换为切片
					// 数组不能 Slice()，所以手动建立了一个切片，这是一种罕见的情况。
					bytes = make([]byte, f.Len())
					for i := range bytes {
						bytes[i] = byte(f.Index(i).Uint())
					}
				}
				// 写入获取到的字节内容
				p.fmtBytes(bytes, verb, t.String())
				return
			}
		}
		if p.fmt.sharpV { // Go 语法格式
			p.buf.WriteString(f.Type().String())
			if f.Kind() == reflect.Slice && f.IsNil() {
				p.buf.WriteString(nilParenString)           // "(nil)"
				return
			} else {
				p.buf.WriteByte('{')
				for i := 0; i < f.Len(); i++ {
					if i > 0 { // 第一个元素前面不添加分隔符
						p.buf.WriteString(commaSpaceString) // ", "
					}
					// 递归处理元素
					p.printValue(f.Index(i), verb, depth+1)
				}
				p.buf.WriteByte('}')
			}
		} else {
			p.buf.WriteByte('[')
			for i := 0; i < f.Len(); i++ {
				if i > 0 { // 第一个元素前面不添加分隔符
					p.buf.WriteByte(' ')
				}
				// 递归处理元素
				p.printValue(f.Index(i), verb, depth+1)
			}
			p.buf.WriteByte(']')
		}
	// 指针（指向具体的对象），需要递归
	case reflect.Ptr:
		// 只处理最外层的指针，不处理嵌套中的指针（避免循环）
		if depth == 0 && f.Pointer() != 0 {
			// 获取指针指向的元素
			switch a := f.Elem(); a.Kind() {
			// 数组、切片、结构、映射
			case reflect.Array, reflect.Slice, reflect.Struct, reflect.Map:
				p.buf.WriteByte('&')
				// 递归处理指针指向的元素
				p.printValue(a, verb, depth+1)
				return
			}
		}
		// 嵌套指针交给下面的代码处理
		fallthrough
	// 通道、函数、不安全指针，只写入指针地址。
	case reflect.Chan, reflect.Func, reflect.UnsafePointer:
		p.fmtPointer(f, verb)
	default:
		p.unknownType(f)
	}
}

// 之后的代码用于解析格式字符串中的“占位符”

// 从指定 arg 中读取整数值（用于提供给精度或宽度）。
// a：arg 列表。argNum：要获取的 arg 下标
// num：获取到的值。isInt：arg 是否为整型。
// newArgNum：成功则返回 argNum+1，失败则返回 argNum。
func intFromArg(a []interface{}, argNum int) (num int, isInt bool, newArgNum int) {
	newArgNum = argNum
	if argNum < len(a) {
		num, isInt = a[argNum].(int) // 通常这里都会转换成功，从而跳过下面一大堆代码。
		if !isInt {
			switch v := reflect.ValueOf(a[argNum]); v.Kind() {			
			// 有符号整型
			case reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64:
				n := v.Int()
				// 根据平台的不同，在 int 范围内进行判断
				if int64(int(n)) == n {
					num = int(n)
					isInt = true
				}
			// 无符号整型
			case reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64, reflect.Uintptr:
				n := v.Uint()
				// 根据平台的不同，在 int 范围内进行判断
				if int64(n) >= 0 && uint64(int(n)) == n {
					num = int(n)
					isInt = true
				}
			default:
				// 默认值 num = 0, isInt = false.
			}
		}
		newArgNum = argNum + 1
		// 范围检查
		if tooLarge(num) {
			num = 0
			isInt = false
		}
	}
	return
}

// 解析 arg 索引，将 [] 中的字符串解析成数值再减 1 后返回。
// format 必须以“[”开头，“]”的位置则会通过查找确定。
// 因为 arg 索引是从 1 开始的，而 arg 下标则是从 0 开始的，所以要减 1。
// index：解析结果。wid：索引字符串的总长度（包括中括号）。ok：是否成功。
// 如果 [] 为空或没有结束括号“]”，则解析失败，index 无效，wid 返回 1。
// wid 返回 1 表示跳过无效的起始括号“[”，继续处理之后的内容。
// 如果 [] 中的内容不全是数字，则解析失败，index 无效，wid 正常返回。
func parseArgNumber(format string) (index int, wid int, ok bool) {
	// 长度至少 3 个字节: [n]。
	if len(format) < 3 {
		return 0, 1, false
	}

	// 查找结束括号“]”
	for i := 1; i < len(format); i++ {
		if format[i] == ']' {
			// 解析 [] 之间的内容。
			// width：解析结果。ok：是否解析成功。newi：待处理下标
			width, ok, newi := parsenum(format, 1, i)
			// 解析失败，或者 n 中含有非法字符。
			if !ok || newi != i {
				return 0, i + 1, false
			}
			// 解析成功
			return width - 1, i + 1, true
		}
	}
	// 没有结束括号“]”
	return 0, 1, false
}

// 解析 format 中的一个 arg 索引，i 标识索引字符串的起始位置，
// 解析过程是通过 parseArgNumber 完成的，这里只进行一些检查、控制等操作。
// argNum：要解析的 arg 下标。numArgs：arg 总个数。
// newArgNum：解析结果。newi：待处理下标。found：是否找到 [] 并且中间全是数字。
func (p *pp) argNumber(argNum int, format string, i int, numArgs int) (newArgNum, newi int, found bool) {
	// format[i] 必须是“[”字符，否则按原值返回，不予处理
	if len(format) <= i || format[i] != '[' {
		return argNum, i, false
	}
	// 标记使用了指定的 argNum，原 argNum 的顺序被打乱。
	p.reordered = true
	// 调用另一个函数去处理 arg 索引
	index, wid, ok := parseArgNumber(format[i:])
	// 解析成功，并且解析出来的 arg 索引在合法范围内。
	if ok && 0 <= index && index < numArgs {
		return index, i + wid, true
	}
	// 解析失败，或者解析出来的 arg 索引超出合法范围。
	// 标记当前 arg 索引无效
	p.goodArgNum = false
	// ok 可能为 true（比如结果超出范围，但这符合 found 的要求）
	return argNum, i + wid, ok
}

// 无效arg索引
func (p *pp) badArgNum(verb rune) {
	p.buf.WriteString(percentBangString) // "%!"
	p.buf.WriteRune(verb)
	p.buf.WriteString(badIndexString)    // "(BADINDEX)"
}

// 未找到相应arg
func (p *pp) missingArg(verb rune) {
	p.buf.WriteString(percentBangString) // "%!"
	p.buf.WriteRune(verb)
	p.buf.WriteString(missingString)     // "(MISSING)"
}

// 解析占位符并格式化相应 arg，以替换占位符。
func (p *pp) doPrintf(format string, a []interface{}) {
	end := len(format)  // 用于范围检查
	argNum := 0         // 正在处理的 arg 索引
	afterIndex := false // 是否刚处理完占位符中的 [n]
	p.reordered = false // 当前 argNum 是否由占位符中 [n] 指定
formatLoop:
	// 每循环一次，处理一个占位符。
	for i := 0; i < end; {
		// 复位有效性，新的 argNum 是有效的
		p.goodArgNum = true

		// 1、写入 % 之前的内容

		lasti := i
		for i < end && format[i] != '%' {
			i++
		}
		if i > lasti {
			p.buf.WriteString(format[lasti:i])
		}
		// 如果到达尾部，则所有占位符都处理完毕，返回
		if i >= end {
			break
		}

		// 跳过 % 字符，正式进入一个“占位符”的处理流程
		i++

		// 复位旗标，准备重新登记
		p.fmt.clearflags()
	simpleFormat:

		// 2、处理旗标 [#0+- ]

		for ; i < end; i++ {
			c := format[i]
			switch c {
			case '#':
				p.fmt.sharp = true
			case '0':
				// 0 只允许填充在左边（不能与 "-" 共存）
				p.fmt.zero = !p.fmt.minus // "-" 优先于 "0"
			case '+':
				p.fmt.plus = true
			case '-':
				// 0 只允许填充在左边（不能与 "-" 共存）
				p.fmt.minus = true
				p.fmt.zero = false        // "-" 优先于 "0"
			case ' ':
				p.fmt.space = true
			default:
				// 没有指定“精度、宽度、[n]”的简单占位符，可以在这里快速处理。
				// 这是常见的占位符，单独处理可以提高效率。
				if 'a' <= c && c <= 'z' && argNum < len(a) {
					if c == 'v' {
						p.fmt.sharpV = p.fmt.sharp // Go 语法格式 #v
						p.fmt.sharp = false        // # 不再具有默认的含义
						p.fmt.plusV = p.fmt.plus   // 结构体字段语法格式 +v
						p.fmt.plus = false         // + 不再具有默认的含义
					}
					p.printArg(a[argNum], rune(c)) // 分析完毕，处理当前 arg
					argNum++                       // 准备处理下一个 arg
					i++                            // 跳过刚处理的动词
					continue formatLoop            // 继续处理下一个占位符
				}
				// 更复杂的占位符（交给后面的代码继续分析）
				break simpleFormat
			}
		}

		// 旗标已经处理过了，接下来处理占位符中的 [n]、宽度、精度信息

		// 处理 [n]（这里处理 [n] 是为后面处理 [n]* 宽度信息做准备，
		// 因为要从相应的 arg 中读取宽度值）
		argNum, i, afterIndex = p.argNumber(argNum, format, i, len(a))
		// argNum：获取到的 [n] 中的值。i：待处理下标。
		// afterIndex：是否刚处理完 [n]

		// 处理从 arg 中获取的宽度信息 *
		// * 表示用 args[argNum] 的值作为宽度值来格式化 args[argNum+1]
		if i < end && format[i] == '*' {
			i++ // 跳过 * 号

			// wid：args[argNum] 的值。widPresent：wid 是否设置成功。
			// argNum：argNum+1（无论 wid 是否设置成功，只要不超出 args 数量）
			p.fmt.wid, p.fmt.widPresent, argNum = intFromArg(a, argNum)

			// 宽度值设置失败，处理无效宽度（仅显示一个提示，不影响其它部分的处理）
			if !p.fmt.widPresent {
				p.buf.WriteString(badWidthString) // "%!(BADWIDTH)"
			}

			// 处理获取到的负宽度值，将其转换为正数，并设置 "-" 旗标为 true
			if p.fmt.wid < 0 {
				p.fmt.wid = -p.fmt.wid
				p.fmt.minus = true
				p.fmt.zero = false // 0 只允许填充在左边（不能与 "-" 共存）
			}
			afterIndex = false // 刚处理的是宽度信息 *，不再是 [n]
		} else {
			// 尽可能解析遇到的数字
			// wid：解析结果。widPresent：是否解析成功。i：待处理下标
			p.fmt.wid, p.fmt.widPresent, i = parsenum(format, i, end)
			// [n] 必须在宽度信息之后（这就是 afterIndex 的作用）
			if afterIndex && p.fmt.widPresent { // 避免 "%[3]2d"
				p.goodArgNum = false
			}
		}

		// 处理精度信息

		if i+1 < end && format[i] == '.' {
			i++ // 跳过小数点
			// [n] 必须在精度信息之后
			// 如果没有设置宽度信息，可能会出现下面的错误写法
			if afterIndex { // 避免 "%[3].2d"
				p.goodArgNum = false
			}

			// 处理 [n]（这里处理 [n] 是为后面处理 [n]* 精度值做准备，
			// 因为要从相应的 arg 中读取精度值）
			argNum, i, afterIndex = p.argNumber(argNum, format, i, len(a))

			// 处理从 arg 中获取的精度信息 *
			// * 表示用 args[argNum] 的值作为精度值来格式化 args[argNum+1]
			if i < end && format[i] == '*' {
				i++ // 跳过 * 号

				// prec：args[argNum] 的值。precPresent：prec 是否设置成功。
				// argNum：argNum+1（无论 prec 是否设置成功，只要不超出 args 数量）
				p.fmt.prec, p.fmt.precPresent, argNum = intFromArg(a, argNum)

				// 负精度值没有意义
				if p.fmt.prec < 0 {
					p.fmt.prec = 0
					p.fmt.precPresent = false
				}
				// 精度值设置失败，处理无效精度（仅显示一个提示，不影响其它部分的处理）
				if !p.fmt.precPresent {
					p.buf.WriteString(badPrecString) // "%!(BADPREC)"
				}
				afterIndex = false // 刚处理的是精度信息 *，不再是 [n]。
			} else {
				// 尽可能解析遇到的数字
				// prec：解析结果。precPresent：是否解析成功。i：待处理下标。
				p.fmt.prec, p.fmt.precPresent, i = parsenum(format, i, end)
				// 如果没有指定精度值，则默认精度为 0。
				if !p.fmt.precPresent {
					p.fmt.prec = 0           // 这个好像不用设置，parsenum 失败则 prec 肯定为 0。
					p.fmt.precPresent = true // 针对 fmt.Printf("%#8.d", 0) 的情况
				// 这里可能是一个小疏忽，忽略了 "%.[3]2d" 这种错误的写法。
				// 下面一行代码经过了修改，并且增加了 2 行代码。
				// [n] 必须在精度值之后。
				} else if afterIndex && !p.fmt.precPresent { // 拒绝 "%.[3]2d"，允许 "%.[2]f"
					p.goodArgNum = false
				}
			}
		}

		// 在宽度信息和精度信息之后可以指定新的 [n]，用以指示要格式化的 arg，所以
		// 这里需要再次获取 [n]。如果之前没有处理宽度和精度信息，那么 [n] 在处理
		// 宽度信息之前就已经获取过了，这里就不需要再次获取。
		if !afterIndex {
			argNum, i, afterIndex = p.argNumber(argNum, format, i, len(a))
		}

		// 如果 arg 索引之后没有了内容，则说明缺少必要的动词
		if i >= end {
			p.buf.WriteString(noVerbString) // "%!(NOVERB)"
			break
		}

		// 获取最后的动词
		verb, w := utf8.DecodeRuneInString(format[i:])
		i += w // 跳过最后的动词

		// 处理特殊动词和错误信息
		switch {
		case verb == '%':      // %% 解析为一个 % 写入
			p.buf.WriteByte('%')
		case !p.goodArgNum:    // argNum 无效（由于 [n] 指定错误，或者放错了位置）
			p.badArgNum(verb)
		case argNum >= len(a): // argNum 超出范围（一般因为 arg 数量不够）
			p.missingArg(verb)
		case verb == 'v':      // 特殊动词 #v 和 +v
			// Go 语法格式
			p.fmt.sharpV = p.fmt.sharp
			p.fmt.sharp = false
			// 结构体字段语法格式
			p.fmt.plusV = p.fmt.plus
			p.fmt.plus = false
			fallthrough
		default:
			// 开始解析 arg
			p.printArg(a[argNum], verb)
			// 准备处理下一个 arg
			argNum++
		}
	}

	// 所有占位符都处理完毕

	// 检查多余的 arg（提供的 arg 过多）
	// 如果在占位符中使用了 [n]，则跳过这里的检查
	if !p.reordered && argNum < len(a) {
		p.fmt.clearflags() // 清空旗标，开始处理多余的 arg
		p.buf.WriteString(extraString)              // "%!(EXTRA "
		for i, arg := range a[argNum:] {
			if i > 0 { // 第一个 arg 之前不添加分隔符
				p.buf.WriteString(commaSpaceString) // ", "
			}
			if arg == nil {
				p.buf.WriteString(nilAngleString)
			} else {
				p.buf.WriteString(reflect.TypeOf(arg).String())
				p.buf.WriteByte('=')
				p.printArg(arg, 'v') // 使用 v 动词处理所有 arg
			}
		}
		p.buf.WriteByte(')')
	}
}

// 格式化 a 中提供的 arg，在非字符串 arg 之间添加空格
func (p *pp) doPrint(a []interface{}) {
	prevString := false
	for argNum, arg := range a {
		isString := arg != nil && reflect.TypeOf(arg).Kind() == reflect.String
		// 在所有非字符串 arg 之间添加空格
		if argNum > 0 && !isString && !prevString {
			p.buf.WriteByte(' ')
		}
		p.printArg(arg, 'v') // 使用 v 动词处理所有 arg
		prevString = isString
	}
}

// 格式化 a 中提供的 arg，在所有 arg 之间添加空格，并在最后添加换行符
func (p *pp) doPrintln(a []interface{}) {
	for argNum, arg := range a {
		// 在所有 arg 之间添加空格
		if argNum > 0 {
			p.buf.WriteByte(' ')
		}
		p.printArg(arg, 'v') // 使用 v 动词处理所有 arg
	}
	p.buf.WriteByte('\n')    // 尾部添加换行符
}
```
