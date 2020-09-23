---
title: Golang学习 - format.go
date: 2016-06-21 16:33:00
updated: 2017-02-21 16:35:25
tags: 
- ubuntu
- pdffonts
categories: 
- linux

---
```golang
// Copyright 2009 The Go Authors. All rights reserved.
// Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

// go/src/fmt/format.go
// version 1.7

package fmt

import (
	"strconv"
	"unicode/utf8"
)


<!--more-->


// 用于进制转换
const (
	ldigits = "0123456789abcdefx"
	udigits = "0123456789ABCDEFX"
)

// 作为参数使用，方便阅读者明白传入的参数是什么含义
const (
	signed   = true
	unsigned = false
)

// 用于记录“占位符”中是否指定了相应的值和旗标
// 单独放在一个结构体中便于清理
type fmtFlags struct {
	widPresent  bool // 宽度值
	precPresent bool // 精度值
	minus       bool // - 旗标
	plus        bool // + 旗标
	sharp       bool // # 旗标
	space       bool // 空格旗标
	zero        bool // 0 旗标

	// 对于特殊格式 %+v 和 %#v，需要特殊处理，单独设置 plusV/sharpV 旗标。
	plusV  bool // +v
	sharpV bool // #v
}

// fmt 是一个基础的格式化器，用于将各种类型的数据进行宽度和精度处理后，
// 写入缓冲区，缓冲区必须单独指定。
type fmt struct {
	buf *buffer // *[]byte

	fmtFlags    // 结构体，定义了许多旗标状态

	wid  int    // 宽度
	prec int    // 精度

	// intbuf 足够存储二进制格式的 int64
	intbuf [68]byte
}

// 复位所有旗标
func (f *fmt) clearflags() {
	f.fmtFlags = fmtFlags{}
}

// 结构体初始化（必须提供缓冲区）
func (f *fmt) init(buf *buffer) {
	f.buf = buf
	f.clearflags()
}

// 写入 n 个字节的填充字符
func (f *fmt) writePadding(n int) {
	if n <= 0 {
		return
	}
	buf := *f.buf
	// 先判断容量，如果容量不足，则进行扩充
	oldLen := len(buf)
	newLen := oldLen + n
	if newLen > cap(buf) {
		// 默认将容量翻倍，但要保证能够容纳写入的内容，所以要 +n
		buf = make(buffer, cap(buf)*2+n)
		copy(buf, *f.buf)
	}
	// 确定要写入的字符
	padByte := byte(' ')
	if f.zero {
		padByte = byte('0')
	}
	// 开始写入
	padding := buf[oldLen:newLen]
	for i := range padding {
		padding[i] = padByte
	}
	// buf 有可能进行了扩充（地址发生了改变），所以要赋值回去
	*f.buf = buf[:newLen]
}

// 下面是写入各种类型的数据。所有写入的内容都会处理宽度和精度信息。

// 写入 []byte
func (f *fmt) pad(b []byte) {
	// 如果没有宽度值，则直接写入
	if !f.widPresent || f.wid == 0 {
		f.buf.Write(b)
		return
	}
	// 宽度值是以字符作为单位，而不是字节。
	width := f.wid - utf8.RuneCount(b)
	if !f.minus {
		// 指定了 '-' 旗标，在左边填充
		f.writePadding(width)
		f.buf.Write(b)
	} else {
		// 未指定 '-' 旗标，在右边填充
		f.buf.Write(b)
		f.writePadding(width)
	}
}

// 写入字符串
func (f *fmt) padString(s string) {
	// 如果没有宽度值，则直接写入
	if !f.widPresent || f.wid == 0 {
		f.buf.WriteString(s)
		return
	}
	// 宽度值是以字符作为单位，而不是字节
	width := f.wid - utf8.RuneCountInString(s)
	if !f.minus {
		// 指定了 '-' 旗标，在左边填充
		f.writePadding(width)
		f.buf.WriteString(s)
	} else {
		// 未指定 '-' 旗标，在右边填充
		f.buf.WriteString(s)
		f.writePadding(width)
	}
}

// 写入布尔值
func (f *fmt) fmt_boolean(v bool) {
	if v {
		f.padString("true")
	} else {
		f.padString("false")
	}
}

// 写入 Unicode 码点
// Unicode 码点格式为 "U+FFFF"，如果指定了 # 旗标，则格式为 "U+FFFF '相应字符'"。
func (f *fmt) fmt_unicode(u uint64) {
	// 临时缓冲区，容量为 68 字节，如果容量不够用，可以进行进行扩充。
	buf := f.intbuf[0:]

	// 1、判断容量是否够用

	// 如果没有指定精度，那么容量肯定够用，因为即便使用 %#U 对 -1 进行格式化，
	// 所需的最大存储空间也只有 18 字节（"U+FFFFFFFFFFFFFFFF"），没有超过 68。
	// 只有指定了过大的精度之后（比如 100），才有可能超出容量范围。
	// 所以下面对容量的判断，只和精度有关。

	// 默认精度为 4（如果码点长度不足 4 位，则添加前导 0，比如 U+0065，如果
	// 码点长度超过精度值，则忽略精度）
	prec := 4

	// 如果指定了精度，则要确保临时缓冲区够用
	if f.precPresent && f.prec > 4 {
		prec = f.prec
		// 估算所需的存储空间："U+"、精度、" '"、相应字符、"'"。
		width := 2 + prec + 2 + utf8.UTFMax + 1
		if width > len(buf) {
			buf = make([]byte, width)
		}
	}

	// 开始格式化

	// 从右向左进行格式化更容易一些
	i := len(buf)

	// 2、处理 # 旗标

	// 在最后添加 '相应字符'。
	// 前提是数值必须在 Unicode 码点范围内，并且字符可打印
	if f.sharp && u <= utf8.MaxRune && strconv.IsPrint(rune(u)) {
		i--
		buf[i] = '\''
		i -= utf8.RuneLen(rune(u))
		utf8.EncodeRune(buf[i:], rune(u))
		i--
		buf[i] = '\''
		i--
		buf[i] = ' '
	}

	// 3、将参数 u 格式化为十六进制数值。

	for u >= 16 {
		i--                     // 确定 buf 的写入下标
		buf[i] = udigits[u&0xF] // 与 1111 相与，获取十六进制的个位数，然后查表取字符。
		prec--                  // 精度被用掉一个
		u >>= 4                 // 丢弃十六进制的个位数
	}
	i--                         // 处理最后一个个位数
	buf[i] = udigits[u]
	prec--

	// 4、处理精度信息（添加前导 0）

	for prec > 0 {
		i--
		buf[i] = '0'
		prec--
	}

	// 5、处理前导 "U+"。

	i--
	buf[i] = '+'
	i--
	buf[i] = 'U'

	// 6、处理宽度信息（用空格填充，不允许用 0 填充）

	oldZero := f.zero
	f.zero = false
	f.pad(buf[i:])
	f.zero = oldZero
}

// 写入整数：包括有符号和无符号，可以指定进位制
// u：要格式化的整数。base：进位制。isSigned：是否有符号。digits：进位制相关字符范围
// 十六进制大小写通过 digits 参数确定
func (f *fmt) fmt_integer(u uint64, base int, isSigned bool, digits string) {

	// 1、修正参数

	// 如果参数 u 中存入的是负值，则将其转变为正值，负号由 negative 保存。
	negative := isSigned && int64(u) < 0
	if negative {
		u = -u // 相当于 -int64(u)，类型转换不会改变值内容，所以减哪个都一样，-u 省一步转换操作
	}

	// 临时缓冲区，容量为 68 字节，如果容量不够用，可以进行进行扩充。
	buf := f.intbuf[0:]

	// 2、确保容量够用

	if f.widPresent || f.precPresent {
		width := 3 + f.wid + f.prec
		// 需要额外的 3 个字节来存放带符号的 "-0x"。
		// 这里为了提高效率，直接将宽度和精度相加（实际上宽度和精度是重叠的），
		// 因为在大多数情况下，相加的结果都不会太大，结果大于 len(buf) 的情况很
		// 少出现，很少需要重新分配内存，所以这里只是为了确保安全而已。相反，如
		// 果用判断语句计算准确的 width 值，反而会降低效率。
		if width > len(buf) {
			buf = make([]byte, width)
		}
	}

	// 3、确定精度信息

	// 注：有两种方式为整数添加前导零：%.3d 或 %08d，
	// 如果同时使用了这两种写法，那么 0 旗标将被忽略，会使用空格实现宽度填充。
	// 也就是说，%08.3d 相当于 %8.3d。

	// 默认精度为 0，如果指定了精度，则使用指定的精度。
	prec := 0
	if f.precPresent {
		prec = f.prec
		// 如果精度指定为 0，值也指定为 0，则表示无内容，只用空格进行填充。
		// 例如：fmt.Printf("%#8.d", 0)
		if prec == 0 && u == 0 {
			oldZero := f.zero
			f.zero = false
			f.writePadding(f.wid)
			f.zero = oldZero
			return
		}
	// 如果没有指定精度，但指定了 0 旗标和宽度，
	// 则将宽度值转换为精度值，由精度处理函数去处理前导 0。
	} else if f.zero && f.widPresent {
		prec = f.wid
		// 如果指定了符号位，则保留一个符号位
		if negative || f.plus || f.space {
			prec--
		}
	}

	// 从右到左进行格式化更容易一些
	i := len(buf)

	// 4、开始编码

	// 使用字面量进行除法和取模操作可以更有效率。
	// case 顺序按照使用频繁度排序。
	switch base {
	case 10:
		for u >= 10 {
			i--                              // 确定缓冲区的写入下标
			next := u / 10                   //去掉个位数
			// 这里用了 - 和 * 求余数，而没有用 %，是不是比 % 更快一些？
			buf[i] = byte('0' + u - next*10) // 获取个位数
			u = next
		}
	case 16:
		for u >= 16 {
			i--                    // 确定缓冲区的写入下标
			buf[i] = digits[u&0xF] // 与 1111 相与，获取十六进制的个位数，然后查表取字符。
			u >>= 4                // 丢弃十六进制的个位数
		}
	case 8:
		for u >= 8 {
			i--                      // 确定缓冲区的写入下标
			buf[i] = byte('0' + u&7) // 与 111 相与，获取八进制的个位数，然后转换为字符。
			u >>= 3                  // 丢弃八进制的个位数
		}
	case 2:
		for u >= 2 {
			i--                      // 确定缓冲区的写入下标
			buf[i] = byte('0' + u&1) // 与 1 相与，获取二进制的个位数，然后转换为字符。
			u >>= 1                  // 丢弃二进制的个位数
		}
	default:
		panic("fmt: unknown base; can't happen") // 未知进位制
	}
	i--                // 最后的个位数还没处理，在这里进行处理
	buf[i] = digits[u] // 所有进制的个位数都可以查表取字符。

	// 5、处理精度信息（添加前导 0）

	for i > 0 && prec > len(buf)-i {
		i--
		buf[i] = '0'
	}

	// 6、处理前缀：0x、0 等

	if f.sharp {
		switch base {
		case 8:
			if buf[i] != '0' {
				i--
				buf[i] = '0'
			}
		case 16:
			// 根据参数 digits 确定大小写：0x、0X
			i--
			buf[i] = digits[16]
			i--
			buf[i] = '0'
		}
	}

	// 7、处理符号位

	if negative {
		i--
		buf[i] = '-'
	} else if f.plus {
		i--
		buf[i] = '+'
	} else if f.space {
		i--
		buf[i] = ' '
	}

	// 8、处理宽度信息（由于指定了精度，所以不能用 0 填充宽度，只能用空格填充）

	oldZero := f.zero
	f.zero = false
	f.pad(buf[i:])
	f.zero = oldZero
}

// 根据精度值截取字符串
func (f *fmt) truncate(s string) string {
	if f.precPresent {
		n := f.prec
		for i := range s {
			n--
			if n < 0 {
				return s[:i]
			}
		}
	}
	return s
}

// 写入字符串
func (f *fmt) fmt_s(s string) {
	s = f.truncate(s)
	f.padString(s)
}

// 写入字符串或字节切片的十六进制格式
func (f *fmt) fmt_sbx(s string, b []byte, digits string) {

	// 1、计算内容长度

	// 获取字符串或切片的长度
	length := len(b)
	if b == nil { // 如果两者都提供了，则优先处理 []byte
		length = len(s)
	}
	// 只处理精度范围内的字节
	if f.precPresent && f.prec < length {
		length = f.prec
	}
	// 每个元素（字节）需要 2 个字节存储其十六进制编码。
	width := 2 * length
	if width > 0 {
		if f.space {
			if f.sharp {
				width *= 2
				// 元素之间有空格，所以需要在每个元素前面都添加 0x 或 0X。
				// 每个元素的十六进制编码刚好是 2 个字节，所以乘以 2 之后，
				// 刚好每个元素多出 2 个字节的空间来存放 0x 或 0X
			}
			// 各个元素之间将被一个空格隔开
			width += length - 1
		} else if f.sharp {
			// 元素之间没有空格，只需要在开头添加一个 0x 或 0X 即可。
			width += 2
		}
	} else { // 元素为空，则仅用空格填充宽度，比如：fmt.Printf("%8x", "")
		if f.widPresent {
			f.writePadding(f.wid)
		}
		return
	}

	// 2、处理“左”宽度填充

	if f.widPresent && f.wid > width && !f.minus {
		f.writePadding(f.wid - width)
	}

	// 3、开始编码

	buf := *f.buf

	// 在第一个元素前面添加前导 0x 或 0X。
	if f.sharp {
		buf = append(buf, '0', digits[16])
	}
	// 遍历各个元素（字节）
	var c byte
	for i := 0; i < length; i++ {
		if f.space && i > 0 {
			// 元素之间添加空格
			buf = append(buf, ' ')
			if f.sharp {
				// 每个元素前面添加 0x 或 0X
				buf = append(buf, '0', digits[16])
			}
		}
		// 对当前元素进行编码
		if b != nil {
			c = b[i] 
		} else {
			c = s[i]
		}
		buf = append(buf, digits[c>>4], digits[c&0xF])
	}

	// 由于 append 操作，缓冲区可能被扩展，需要赋值回去
	*f.buf = buf

	// 4、处理“右”宽度填充

	if f.widPresent && f.wid > width && f.minus {
		f.writePadding(f.wid - width)
	}
}

// 写入字符串的十六进制格式
func (f *fmt) fmt_sx(s, digits string) {
	f.fmt_sbx(s, nil, digits)
}

// 写入字节切片的十六进制格式
func (f *fmt) fmt_bx(b []byte, digits string) {
	f.fmt_sbx("", b, digits)
}

// 写入双引号字符串。
// 如果指定了 # 旗标，而且字符串不包含任何控制字符（除制表符），
// 则会写入反引号原始字符串。
// 如果指定了 + 旗标，则字符串中的所有非 ASCII 字符都会被转义处理。
func (f *fmt) fmt_q(s string) {

	// 1、处理精度信息

	// 将字符串截断到指定精度
	s = f.truncate(s)

	// 2、开始编码

	// 处理 # 号
	if f.sharp && strconv.CanBackquote(s) {
		f.padString("`" + s + "`")
		return
	}

	// 临时缓冲区，提供给下面的转换函数使用。
	buf := f.intbuf[:0]
	// 转换结果不一定在这个 buf 中，因为转换过程中可能会扩容。
	// 转换结果在转换函数的返回值中，所以这个 buf 仅仅是一个参数。

	// 转换并写入
	if f.plus {
		// 非 ASCII 字符将被转换为 Unicode 码点
		f.pad(strconv.AppendQuoteToASCII(buf, s))
	} else {
		// 非 ASCII 字符将正常输出
		f.pad(strconv.AppendQuote(buf, s))
	}
}

// 写入单个字符
// 如果字符不是有效的 Unicode 编码，则写入 '\ufffd'
func (f *fmt) fmt_c(c uint64) {
	r := rune(c)
	// 超出 Unicode 范围
	if c > utf8.MaxRune {
		r = utf8.RuneError
	}
	// 临时缓冲区
	buf := f.intbuf[:0]
	// 对 r 进行编码
	w := utf8.EncodeRune(buf[:utf8.UTFMax], r)
	// 写入编码结果
	f.pad(buf[:w])
}

// 写入单引号字符
// 如果字符不是有效的 Unicode 编码，则写入 '\ufffd'
// 如果指定了 + 旗标，则非 ASCII 字符会被转义处理。
func (f *fmt) fmt_qc(c uint64) {
	r := rune(c)
	// 超出 Unicode 范围
	if c > utf8.MaxRune {
		r = utf8.RuneError
	}
	// 临时缓冲区
	buf := f.intbuf[:0]
	// 编码并处理宽度信息
	if f.plus {
		// 非 ASCII 字符将被转换为 Unicode 码点
		f.pad(strconv.AppendQuoteRuneToASCII(buf, r))
	} else {
		// 非 ASCII 字符将被正常输出
		f.pad(strconv.AppendQuoteRune(buf, r))
	}
}

// 写入 float64
func (f *fmt) fmt_float(v float64, size int, verb rune, prec int) {

	// 1、开始编码

	// “占位符”中的精度将覆盖参数中的默认精度
	if f.precPresent {
		prec = f.prec
	}
	// 格式化数值，结果写入临时缓冲区，为 + 号预留一个空格
	// 如果 verb 是一个有效的动词，则可以在这里将其转换为字节类型传入。
	num := strconv.AppendFloat(f.intbuf[:1], v, byte(verb), prec, size)

	// 2、处理符号

	// 如果转换结果中有符号，则去掉预留的空格，否则将预留的空格转换为 + 号
	if num[1] == '-' || num[1] == '+' {
		num = num[1:]
	} else {
		num[0] = '+'
	}
	// 如果指定了 " " 旗标，而没有指定 "+" 旗标，则将 + 号改为空格。
	if f.space && num[0] == '+' && !f.plus {
		num[0] = ' '
	}

	// 3、处理无穷大和非数字

	// 它看起来不像一个数字，所以不应该用 0 填充。
	if num[1] == 'I' || num[1] == 'N' {
		oldZero := f.zero
		f.zero = false
		// 如果没有指定符号，则移除 NaN 前面的符号
		if num[1] == 'N' && !f.space && !f.plus {
			num = num[1:]
		}
		f.pad(num)
		f.zero = oldZero
		return
	}

	// 4、开始写入

	// 指定了 "+" 旗标 或 结果不是以 + 开头（以 - 或空格开头）。
	if f.plus || num[0] != '+' {
		// 如果用 0 进行了左填充，那么我们希望符号在所有 0 的前面。
		if f.zero && f.widPresent && f.wid > len(num) {
			f.buf.WriteByte(num[0])          // 写入符号
			f.writePadding(f.wid - len(num)) // 进行填充
			f.buf.Write(num[1:])             // 写入符号之外的内容
			return
		}
		f.pad(num)
		return
	}
	// 未指定 "+" 旗标，但结果是以 + 号开头，则去掉 + 号后写入。
	f.pad(num[1:])
}
```
