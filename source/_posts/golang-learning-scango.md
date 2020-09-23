---
title: Golang学习 - scan.go
date: 2016-07-19 16:36:00
updated: 2017-02-21 16:38:23
tags: 
- vscode
categories: 
- linux

---
```golang
// Copyright 2010 The Go Authors. All rights reserved.
// Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

// go/src/fmt/scan.go
// version 1.7

package fmt

import (
	"errors"
	"io"
	"math"
	"os"
	"reflect"
	"strconv"
	"sync"
	"unicode/utf8"
)

// ScanState 将扫描器的状态报告给自定义类型的 Scan 方法。
type ScanState interface {
	// ReadRune 从输入端读取一个字符，如果用在 Scanln 类的扫描器中，
	// 则该方法会在读到第一个换行符之后或读到指定宽度之后返回 EOF。
	// r   ：读取的字符
	// size：字符所占用的字节数
	// err ：遇到的错误信息
	ReadRune() (r rune, size int, err error)
	// UnreadRune 撤消最后一次的 ReadRune 操作，
	// 使下次的 ReadRune 操作得到与前一次 ReadRune 相同的结果。
	// 返回：遇到的错误信息
	UnreadRune() error
	// SkipSpace 为自定义的 Scan 方法提供跳过开头空白的能力。
	// 根据扫描器的不同（Scan 或 Scanln）决定是否跳过换行符。
	SkipSpace()
	// Token 用于从输入端读取符合要求的字符串，准备解析。
	// Token 从输入端读取连续的符合 f(c) 的字符 c。如果 f 为 nil，则使用
	// !unicode.IsSpace(c) 代替 f(c)。
	// skipSpace：是否跳过输入端开头的连续空白（通过 SkipSpace 方法）。
	// token    ：存放读取到的数据。
	// err      ：遇到的错误信息。
	// 注意：token 指向共享的数据，下次的 Token 操作可能会覆盖本次的结果。
	Token(skipSpace bool, f func(rune) bool) (token []byte, err error)
	// Width 返回占位符中指定的宽度值（宽度值是字符个数，不是字节个数）。
	// wid：获取到的宽度值
	// ok ：是否指定了宽度值
	Width() (wid int, ok bool)
	// 因为上面实现了 ReadRune 方法，所以 Read 方法永远不应该被 Scan 方法调用。
	// 一个好的 ScanState 应该让 Read 直接返回相应的错误信息。
	Read(buf []byte) (n int, err error)
}

// Scanner 用于让自定义类型实现自己的扫描过程。
// Scan 方法会从输入端读取数据并将处理结果存入接收端，接收端必须是有效的指针。
// Scan 方法会被扫描器调用，只要对应的 arg 实现了该方法。
type Scanner interface {
	Scan(state ScanState, verb rune) error
}

// Scan 从标准输入中读取字符串（以空白分隔的值的序列）并解析为具体的值，
// 存入参数 a 所提供的变量中（参数 a 必须提供变量的地址）。换行视为空白。
// 当读到 EOF 或所有变量都填写完毕则停止扫描。
// n  ：成功解析的参数数量
// err：解析过程中遇到的错误信息
func Scan(a ...interface{}) (n int, err error) {
	return Fscan(os.Stdin, a...)
}

// Scanln 和 Scan 类似，只不过遇到换行符就停止扫描。
func Scanln(a ...interface{}) (n int, err error) {
	return Fscanln(os.Stdin, a...)
}

// Scanf 从标准输入中读取字符串，并根据格式字符串 format 对读取的数据进行解析，
// 存入参数 a 所提供的变量中（参数 a 必须提供变量的地址）。
// 输入端的换行符必须和格式字符串中的换行符相对应（如果格式字符串中有换行符，则
// 输入端必须输入相应的换行符）。
// 占位符 %c 总是匹配下一个字符，包括空白，比如空格符、制表符、换行符。
// n  ：成功解析的参数数量
// err：解析过程中遇到的错误信息
func Scanf(format string, a ...interface{}) (n int, err error) {
	return Fscanf(os.Stdin, format, a...)
}

// 实现了 Reader 接口的字符串类型
type stringReader string

func (r *stringReader) Read(b []byte) (n int, err error) {
	n = copy(b, *r)
	*r = (*r)[n:]
	if n == 0 {
		err = io.EOF
	}
	return
}

// Sscan 和 Scan 类似，只不过从 str 中读取数据。
func Sscan(str string, a ...interface{}) (n int, err error) {
	return Fscan((*stringReader)(&str), a...)
}

// Sscanln 和 Scanln 类似，只不过从 str 中读取数据。
func Sscanln(str string, a ...interface{}) (n int, err error) {
	return Fscanln((*stringReader)(&str), a...)
}

// Sscanf 和 Scanf 类似，只不过从 str 中读取数据。
func Sscanf(str string, format string, a ...interface{}) (n int, err error) {
	return Fscanf((*stringReader)(&str), format, a...)
}

// Fscan 和 Scan 类似，只不过从 r 中读取数据。
func Fscan(r io.Reader, a ...interface{}) (n int, err error) {
	s, old := newScanState(r, true, false) // 创建扫描器
	n, err = s.doScan(a)                   // 开始扫描
	s.free(old)                            // 回收扫描器
	return
}

// Fscanln 和 Fcanln 类似，只不过从 r 中读取数据。
func Fscanln(r io.Reader, a ...interface{}) (n int, err error) {
	s, old := newScanState(r, false, true) // 创建扫描器
	n, err = s.doScan(a)                   // 开始扫描
	s.free(old)                            // 回收扫描器
	return
}

// Fscanf 和 Scanf 类似，只不过从 r 中读取数据。
func Fscanf(r io.Reader, format string, a ...interface{}) (n int, err error) {
	s, old := newScanState(r, false, false) // 创建扫描器
	n, err = s.doScanf(format, a)           // 开始扫描
	s.free(old)                             // 回收扫描器
	return
}

// scanError 声明本地错误类型，用于 recover 时辨别 panic 是否由本地代码产生的。
type scanError struct {
	err error
}

// 本地代码用 -1 表示遇到 EOF
const eof = -1

// ss 是扫描器，整个扫描过程都是由它完成的。
// 它从 rs 中读取数据并进行解析。
type ss struct {
	rs    io.RuneScanner // 输入端
	buf   buffer         // 缓冲区
	count int            // 已读取的字符数
	atEOF bool           // 是否读到 EOF
	ssave                // 一些需要经常复位的字段
}

// ssave 是 ss 的一部分，存储一些需要经常复位的字段
type ssave struct {
	validSave bool // 平时用不上，递归时使用
	nlIsEnd   bool // 是否在换行符之后停止读取
	nlIsSpace bool // 是否将换行符视为空白
	argLimit  int  // 已读的字符数不能超过 argLimit（argLimit <= limit）
	limit     int  // 已读的字符数不能超过 limit（好像就当做常量在使用，用于复位 argLimit）
	maxWid    int  // 存储占位符中指定的宽度值
}

// 实现 ScanState 接口
// Read 方法仅用于 ScanState 以满足 io.Reader 接口。
// 在内部永远不会调用它，所以没有必要让它有任何动作。
func (s *ss) Read(buf []byte) (n int, err error) {
	return 0, errors.New("ScanState's Read should not be called. Use ReadRune")
}

// 实现 ScanState 接口
func (s *ss) ReadRune() (r rune, size int, err error) {
	// 读到 EOF 或超出读取限制，则返回 0 0 nil
	if s.atEOF || s.count >= s.argLimit {
		err = io.EOF
		return
	}
	r, size, err = s.rs.ReadRune()
	if err == nil {
		s.count++ // 统计被读出的字符数
		if s.nlIsEnd && r == '\n' {
			s.atEOF = true // 拒绝再次 ReadRune
		}
	} else if err == io.EOF {
		s.atEOF = true // 拒绝再次 ReadRune
	}
	return
}

// 实现 ScanState 接口
func (s *ss) Width() (wid int, ok bool) {
	if s.maxWid == hugeWid { //	hugeWid 是常量 1 << 30
		return 0, false
	}
	return s.maxWid, true
}

// 读取一个字符，如果遇到 EOF 则返回 eof（即 -1）
// 如果遇到其它错误，则中止整个扫描过程，返回 err。
func (s *ss) getRune() (r rune) {
	r, _, err := s.ReadRune()
	if err != nil {
		if err == io.EOF {
			return eof
		}
		s.error(err)
	}
	return
}

// 功能同 getRune，只不过遇到 EOF 也中止整个扫描过程，返回 err。
func (s *ss) mustReadRune() (r rune) {
	r = s.getRune()
	if r == eof {
		s.error(io.ErrUnexpectedEOF)
	}
	return
}

// 实现 ScanState 接口
func (s *ss) UnreadRune() error {
	s.rs.UnreadRune()
	s.atEOF = false // 允许再次 ReadRune
	s.count--       // 统计被读出的字符数
	return nil      // UnreadRune 可以反复调用，不返回错误信息。
}

// 将错误信息转换为 panic。
// 用于配合 recover 快速结束函数调用链，避免过多的返回值判断。
// 类似于 break label 的用法。
func (s *ss) error(err error) {
	panic(scanError{err})
}

// 作用同上面的 error 方法
func (s *ss) errorString(err string) {
	panic(scanError{errors.New(err)})
}

// 实现 ScanState 接口
func (s *ss) Token(skipSpace bool, f func(rune) bool) (tok []byte, err error) {
	// 遇到本地错误则仅仅返回 err 信息。
	// 遇到其它错误则 panic。
	defer func() {
		if e := recover(); e != nil {
			if se, ok := e.(scanError); ok {
				err = se.err
			} else {
				panic(e)
			}
		}
	}()
	// 确定审查函数
	if f == nil {
		f = notSpace
	}
	// 准备缓冲区给 s.token 用
	s.buf = s.buf[:0]
	tok = s.token(skipSpace, f)
	return
}

// space 是 unicode.White_Space 的拷贝，避免包的深度依赖。
// 这些都是空白字符的 Unicode 码点范围
var space = [][2]uint16{
	{0x0009, 0x000d},
	{0x0020, 0x0020},
	{0x0085, 0x0085},
	{0x00a0, 0x00a0},
	{0x1680, 0x1680},
	{0x2000, 0x200a},
	{0x2028, 0x2029},
	{0x202f, 0x202f},
	{0x205f, 0x205f},
	{0x3000, 0x3000},
}

// 判断 r 是否为空白字符
func isSpace(r rune) bool {
	// 空白字符的码点不会超过 2 个字节
	if r >= 1<<16 {
		return false
	}
	// 开始判断
	rx := uint16(r)
	for _, rng := range space {
		if rx < rng[0] {
			return false
		}
		if rx <= rng[1] {
			return true
		}
	}
	return false
}

// notSpace 是 Token 中的默认审查函数。
func notSpace(r rune) bool {
	return !isSpace(r)
}

// 实现 ScanState 接口
func (s *ss) SkipSpace() {
	s.skipSpace(false)
}

// readRune 用于将 io.Reader 包装成 io.RuneScanner
type readRune struct {
	reader   io.Reader         // 被包装的 io.Reader
	buf      [utf8.UTFMax]byte // 仅在 ReadRune 方法中使用
	pending  int               // pendBuf 中存放的字节数，遇到无效 UTF8 编码时使用。
	pendBuf  [utf8.UTFMax]byte // 存放读取的无效 UTF-8 编码，一次处理不完，留着下次处理
	peekRune rune              // 用于 UnreadRune 存放撤销的字符。
}

// readByte 读取一个字节
// 它可能是上次 ReadRune 时未处理完的不完整 UTF8 编码。
func (r *readRune) readByte() (b byte, err error) {
	// 如果之前的 ReadRune 有未处理完的字节，则重新读出这些字节。
	if r.pending > 0 {
		// 读出一个字节
		b = r.pendBuf[0]
		// 剩下的字节向前移动一格
		copy(r.pendBuf[0:], r.pendBuf[1:])
		r.pending--
		return
	}
	// 如果没有未处理的字节，则从输入端读出一个字节
	n, err := io.ReadFull(r.reader, r.pendBuf[:1])
	if n != 1 {
		return 0, err
	}
	return r.pendBuf[0], err
}

// 实现 io.RuneScanner 接口
func (r *readRune) ReadRune() (rr rune, size int, err error) {
	// 之前 UnreadRune 撤销的字符，存在 peekRune 中，有则直接取出。
	if r.peekRune >= 0 {
		rr = r.peekRune
		// 将 peekRune 取反为负数，表示允许 UnreadRune 执行撤销操作
		r.peekRune = ^r.peekRune
		size = utf8.RuneLen(rr)
		return
	}
	// 没有撤销的字符，则从输入端读取一个
	r.buf[0], err = r.readByte()
	if err != nil {
		return
	}
	// 如果读出的是一个单字节字符，则读取完毕。
	if r.buf[0] < utf8.RuneSelf {
		rr = rune(r.buf[0])
		size = 1
		// 将读出的内容写入 peekRune 后取反，以便 UnreadRune 可以撤销。
		r.peekRune = ^rr
		return
	}
	// 读出的不是单字节字符
	var n int
	// FullRune 的功能不太好理解，总的来说，就是判断首字符的编码长度是否完整，
	// 如果不完整则返回 false，其它情况都返回 true（包括无效编码）。
	// 循环直到 r.buf[:n] 是完整的 UTF-8 编码（或无效编码）
	for n = 1; !utf8.FullRune(r.buf[:n]); n++ {
		// 如果字符编码长度不够，则再读出一个字节，继续判断
		r.buf[n], err = r.readByte()
		if err != nil {
			if err == io.EOF {
				err = nil // 之前有读出的字节未处理，跳出去处理
				break
			}
			return
		}
	}
	// 解码刚读出的 UTF-8 序列
	rr, size = utf8.DecodeRune(r.buf[:n])
	if size < n {
		// 遇到错误，保存未处理的字节，用于下一次读取。
		copy(r.pendBuf[r.pending:], r.buf[size:n])
		r.pending += n - size
	}
	// 将读出的内容写入 peekRune 后取反，以便 UnreadRune 可以撤销。
	r.peekRune = ^rr
	return
}

// 实现 io.RuneScanner 接口
func (r *readRune) UnreadRune() error {
	// 之前执行过 UnreadRune，不能重复执行。
	// 只有 UnreadRune 才能让 peekRune 大于 0。
	if r.peekRune >= 0 {
		return errors.New("fmt: scanning called UnreadRune with no rune available")
	}
	// 开始撤销
	// 反转 peekRune 中的二进制位，使其成为有效的字符。
	r.peekRune = ^r.peekRune
	return nil
}

// 临时对象池
var ssFree = sync.Pool{
	New: func() interface{} { return new(ss) },
}

// 创建扫描器，或从临时对象池中获取一个。
func newScanState(r io.Reader, nlIsSpace, nlIsEnd bool) (s *ss, old ssave) {
	// 从临时对象池中获取一个扫描器
	s = ssFree.Get().(*ss)
	// 如果参数 r 不是一个 RuneScanner，则将其包装成 RuneScanner 再赋值给 s.rs
	if rs, ok := r.(io.RuneScanner); ok {
		s.rs = rs
	} else {
		// 注意：r 只有 Read 方法，没有撤销方法，所以这里包装的 readRune 对象
		// 无法将 UnreadRune 所撤销的内容返回到 r 中。也就是说，尽量为 r 提供
		// RuneScanner，否则可能造成 r 中的数据丢失。
		s.rs = &readRune{reader: r, peekRune: -1}
	}
	// 复位参数
	s.nlIsSpace = nlIsSpace
	s.nlIsEnd = nlIsEnd
	s.atEOF = false
	s.limit = hugeWid
	s.argLimit = hugeWid
	s.maxWid = hugeWid
	s.validSave = true
	s.count = 0
	return
}

// 回收扫描器，避免再次分配。
func (s *ss) free(old ssave) {
	// 如果扫描器被递归使用，则只需要恢复旧状态，然后继续使用。
	if old.validSave {
		s.ssave = old
		return
	}
	// 不回收缓冲区太大的扫描器，避免内存浪费。
	if cap(s.buf) > 1024 {
		return
	}
	// 复位并存入
	s.buf = s.buf[:0]
	s.rs = nil
	ssFree.Put(s)
}

// 用于实现 ScanState 接口
func (s *ss) skipSpace(stopAtNewline bool) {
	for {
		r := s.getRune()
		if r == eof {
			return
		}
		// \r\n 当 \n 处理
		// peek 判断即将读取的字符是否在字符串 "\n" 中（只判断不读取）。
		if r == '\r' && s.peek("\n") {
			continue
		}
		if r == '\n' {
			if stopAtNewline { // 换行符之后停止读取
				break
			}
			if s.nlIsSpace { // 换行符当空白处理
				continue
			}
			// 换行符当非空白字符处理
			// 在这里不允许，所以中止整个扫描过程，返回 err。
			s.errorString("unexpected newline")
			return
		}
		// 非空白字符，撤销读取并返回。
		if !isSpace(r) {
			s.UnreadRune()
			break
		}
	}
}

// 用于实现 ScanState 接口
func (s *ss) token(skipSpace bool, f func(rune) bool) []byte {
	if skipSpace {
		s.skipSpace(false)
	}
	// 循环读取直到不满足 f(r) 或遇到 EOF
	for {
		r := s.getRune()
		if r == eof {
			break
		}
		if !f(r) {
			s.UnreadRune()
			break
		}
		s.buf.WriteRune(r)
	}
	return s.buf
}

var complexError = errors.New("syntax error scanning complex number")
var boolError = errors.New("syntax error scanning boolean")

// 返回 r 在 s 中的字符序号（不是字节下标）
func indexRune(s string, r rune) int {
	for i, c := range s {
		if c == r {
			return i
		}
	}
	return -1
}

// 判断即将读取的字符是否在 ok 中。
// 如果 accept 为 flase 则读取并丢弃该字符，无论结果如何。
// 如果 accept 为 true，则根据结果做进一步处理：
// 结果为 true ：将字符读入 s.buf 中
// 结果为 false：不读取该字符
func (s *ss) consume(ok string, accept bool) bool {
	r := s.getRune()
	if r == eof {
		return false
	}
	// r 在 ok 中
	if indexRune(ok, r) >= 0 {
		if accept {
			s.buf.WriteRune(r)
		}
		return true
	}
	// r 不在 ok 中（上面已经判断过 r == eof，所以这里没必要再次判断）
	if r != eof && accept {
		s.UnreadRune() // 不读取该字符
	}
	return false
}

// 判断即将读取的字符是否在 ok 中，但不读取该字符。
func (s *ss) peek(ok string) bool {
	r := s.getRune()
	if r != eof {
		s.UnreadRune()
	}
	// 在 ok 中查找 r 的下标，判断您是否 >= 0
	return indexRune(ok, r) >= 0
}

// 判断输入端是否有数据可读
// 如果没有数据可读，则中止整个扫描过程，返回 err。
func (s *ss) notEOF() {
	if r := s.getRune(); r == eof {
		panic(io.EOF)
	}
	s.UnreadRune()
}

// 判断即将读取的字符是否在 ok 中，如果在，则将其读入 s.buf 中，
// 并返回 true，否则不读取，并返回 false。
func (s *ss) accept(ok string) bool {
	return s.consume(ok, true)
}

// 判断 verb 是否在 okVerbs 中，
// 如果在，则返回 true。如果不在，则中止整个扫描过程，返回 err。
// 没有返回 false 的情况。typ 用于在 err 中指示类型信息。
func (s *ss) okVerb(verb rune, okVerbs, typ string) bool {
	for _, v := range okVerbs {
		if v == verb {
			return true
		}
	}
	s.errorString("bad verb '%" + string(verb) + "' for " + typ)
	return false
}

// 从输入端读取一个布尔值，verb 必须为 t 或 v，否则读取失败。
// 可探测 0、1、t、f、true、false，忽略大小写。
func (s *ss) scanBool(verb rune) bool {
	// 跳过行首空白（包括换行符）
	s.skipSpace(false)
	// 输入端必须有内容可读
	s.notEOF()
	// 动词不是 t 或 v，不符合布尔型的要求
	if !s.okVerb(verb, "tv", "boolean") {
		return false
	}
	// 布尔型的语法检测很讨厌，我们不做严格要求。
	// 如果遇到不完整的 tr、tru 或 fa、fal、fals 则中止整个扫描过程，返回 err。
	switch s.getRune() {
	case '0':
		return false
	case '1':
		return true
	case 't', 'T':
		if s.accept("rR") && (!s.accept("uU") || !s.accept("eE")) {
			s.error(boolError)
		}
		return true
	case 'f', 'F':
		if s.accept("aA") && (!s.accept("lL") || !s.accept("sS") || !s.accept("eE")) {
			s.error(boolError)
		}
		return false
	}
	return false
}

// 数值元素
const (
	binaryDigits      = "01"
	octalDigits       = "01234567"
	decimalDigits     = "0123456789"
	hexadecimalDigits = "0123456789aAbBcCdDeEfF"
	sign              = "+-"
	period            = "."
	exponent          = "eEp"
)

// 返回 verb 所代表的进位制，及其字符范围（即上面的常量）
func (s *ss) getBase(verb rune) (base int, digits string) {
	// 判断 verb 是否符合整型要求。
	// 如果不符合，则中止整个扫描过程，返回 err。
	s.okVerb(verb, "bdoUxXv", "integer")
	base = 10 // 默认为十进制
	digits = decimalDigits
	switch verb {
	case 'b': // 二进制
		base = 2
		digits = binaryDigits
	case 'o': // 八进制
		base = 8
		digits = octalDigits
	case 'x', 'X', 'U': // 十六进制
		base = 16
		digits = hexadecimalDigits
	}
	return
}

// 从输入端读取数值字符串到 s.buf 中。
// digits 是可接收的字符范围（不同进位制有不同的字符范围）
// haveDigits 表示 s.buf 中是否已经有数值存在，
// 如果没有，则本方法必须读出数值，否则中止整个扫描过程，返回 err。
func (s *ss) scanNumber(digits string, haveDigits bool) string {
	if !haveDigits {
		// 输入端必须有内容可读
		s.notEOF()
		if !s.accept(digits) {
			// 如果没有读到指定进制的字符，则中止整个扫描过程，返回 err。
			s.errorString("expected integer")
		}
	}
	// 继续读取合格的字符，存入 s.buf 中
	for s.accept(digits) {
	}
	// 返回读出的字符串
	return string(s.buf)
}

// 功能同 ReadRune，只不过通过 bitSize 限制读取字符的位宽。
// 如果读出的字符在指定位宽内，则返回，否则中止整个扫描过程，返回 err。
func (s *ss) scanRune(bitSize int) int64 {
	s.notEOF()
	r := int64(s.getRune())
	n := uint(bitSize)
	// 位宽判断
	x := (r << (64 - n)) >> (64 - n)
	if x != r {
		s.errorString("overflow on character value " + string(r))
	}
	return r
}

// 根据输入端的前导符 0 或 0x 判断进位制并返回，同时返回字符范围。
// found 表示检测到前导符。只有当动词是 %v 的时候才会被调用。
func (s *ss) scanBasePrefix() (base int, digits string, found bool) {
	// 如果不是以 0 开头，表示是十进制数
	if !s.peek("0") {
		return 10, decimalDigits, false
	}
	// 如果是 0 开头，则将其读入 s.buf 中
	s.accept("0")
	found = true // 已经读出一个 0，如果前导符后面没有数值，将使用该 0 值。
	// 继续判断是八进制还是十六进制
	base, digits = 8, octalDigits
	if s.peek("xX") {
		s.consume("xX", false) // 丢弃匹配的 x 或 X 字符
		base, digits = 16, hexadecimalDigits
	}
	return
}

// 读取一个 int64 整数。bitSize 用于限制整数的位宽。
// 如果读出的整数在指定位宽内，则返回，否则中止整个扫描过程，返回 err。
func (s *ss) scanInt(verb rune, bitSize int) int64 {
	// 只需要读取一个字符
	if verb == 'c' {
		return s.scanRune(bitSize)
	}
	s.skipSpace(false)
	s.notEOF()
	// 根据不同的动词获取进位制信息
	base, digits := s.getBase(verb)
	haveDigits := false // 是否已经读出数值
	if verb == 'U' {
		// 丢弃前导符 U+
		// 如果没有读取到 U+ 则中止整个扫描过程，返回 err。
		if !s.consume("U", false) || !s.consume("+", false) {
			s.errorString("bad unicode format ")
		}
	} else {
		// sign 是常量 +-，如果能读取到符号，则将其存入 s.buf 中。
		s.accept(sign)
		if verb == 'v' {
			// 根据输入端的前导符 0 或 0x 确定进位制
			// 如果有前导符，则已经读出一个 0，前导符后面可以没有数值。
			base, digits, haveDigits = s.scanBasePrefix()
		}
	}
	// 读出数值字符串，如果读取失败，则中止整个扫描过程，返回 err。
	tok := s.scanNumber(digits, haveDigits)
	// 转换为整型
	i, err := strconv.ParseInt(tok, base, 64)
	if err != nil {
		s.error(err)
	}
	// 位宽判断
	n := uint(bitSize)
	x := (i << (64 - n)) >> (64 - n)
	if x != i {
		s.errorString("integer overflow on token " + tok)
	}
	return i
}

// 功能同 scanInt，只不过返回的是无符号整数。
func (s *ss) scanUint(verb rune, bitSize int) uint64 {
	if verb == 'c' {
		return uint64(s.scanRune(bitSize))
	}
	s.skipSpace(false)
	s.notEOF()
	base, digits := s.getBase(verb)
	haveDigits := false
	if verb == 'U' {
		if !s.consume("U", false) || !s.consume("+", false) {
			s.errorString("bad unicode format ")
		}
	} else if verb == 'v' {
		base, digits, haveDigits = s.scanBasePrefix()
	}
	tok := s.scanNumber(digits, haveDigits)
	i, err := strconv.ParseUint(tok, base, 64)
	if err != nil {
		s.error(err)
	}
	n := uint(bitSize)
	x := (i << (64 - n)) >> (64 - n)
	if x != i {
		s.errorString("unsigned integer overflow on token " + tok)
	}
	return i
}

// 读取一个浮点数，如果指定了宽度值，则不会超过宽度值。
// 没有检查“只有指数没有小数”的情况，但是 Atof 会进行检查。
func (s *ss) floatToken() string {
	s.buf = s.buf[:0]
	// 非数值 NAN
	if s.accept("nN") && s.accept("aA") && s.accept("nN") {
		return string(s.buf)
	}
	// 符号 +-
	s.accept(sign)
	// 无穷大 INF
	if s.accept("iI") && s.accept("nN") && s.accept("fF") {
		return string(s.buf)
	}
	// 整数部分
	for s.accept(decimalDigits) {
	}
	// 小数点
	if s.accept(period) {
		// 小数部分
		for s.accept(decimalDigits) {
		}
	}
	// 指数标志
	if s.accept(exponent) {
		// 指数符号
		s.accept(sign)
		// 指数值
		for s.accept(decimalDigits) {
		}
	}
	return string(s.buf)
}

// 读出一个虚数的实部和虚部。
// 虚数可以加上括号，虚数格式必须为 N+Ni，N 必须是浮点数，中间不能有空格。
func (s *ss) complexTokens() (real, imag string) {
	// TODO: 未实现纯实部和纯虚部的读取
	parens := s.accept("(") // 登记是否以 "(" 开头
	real = s.floatToken()   // 读取实部
	s.buf = s.buf[:0]
	// 虚部必须有符号
	if !s.accept("+-") { // 读取符号到 s.buf
		s.error(complexError)
	}
	imagSign := string(s.buf) // 取出符号
	imag = s.floatToken()     // 读取虚部
	if !s.accept("i") {       // 虚部后面必须为 i
		s.error(complexError)
	}
	// 如果以 "(" 开头，则必须以 ")" 结尾。
	if parens && !s.accept(")") {
		s.error(complexError)
	}
	return real, imagSign + imag
}

// 将一个字符串转换为 float64 类型的值。
// str 要转换的字符串，n：要转换出的浮点数类型（32 或 64）
// 如果转换失败，则中止整个扫描过程，返回 err。
func (s *ss) convertFloat(str string, n int) float64 {
	// Atof 不处理以 2 为底的指数，但是它们很容易计算。
	if p := indexRune(str, 'p'); p >= 0 {
		// 获取小数部分
		f, err := strconv.ParseFloat(str[:p], n)
		if err != nil {
			if e, ok := err.(*strconv.NumError); ok {
				e.Num = str
			}
			s.error(err)
		}
		// 获取指数部分
		m, err := strconv.Atoi(str[p+1:])
		if err != nil {
			//
			if e, ok := err.(*strconv.NumError); ok {
				e.Num = str
			}
			s.error(err)
		}
		// 算出结果：f * (2 的 m 次方)
		return math.Ldexp(f, m)
	}
	// 普通浮点数直接转换
	f, err := strconv.ParseFloat(str, n)
	if err != nil {
		s.error(err)
	}
	return f
}

// 读取一个 complex128 类型的值。
func (s *ss) scanComplex(verb rune, n int) complex128 {
	// 检查 verb 的有效性（floatVerbs 是常量 "beEfFgGv"）
	if !s.okVerb(verb, floatVerbs, "complex") {
		return 0
	}
	s.skipSpace(false)
	s.notEOF()
	// 读取实部和虚部
	sreal, simag := s.complexTokens()
	real := s.convertFloat(sreal, n/2)
	imag := s.convertFloat(simag, n/2)
	return complex(real, imag)
}

// 读取一个字符串。
func (s *ss) convertString(verb rune) (str string) {
	// 检查 verb 的有效性
	if !s.okVerb(verb, "svqxX", "string") {
		return ""
	}
	s.skipSpace(false)
	s.notEOF()
	switch verb {
	case 'q': // 带引号字符串
		str = s.quotedString()
	case 'x', 'X': // 十六进制格式的字符串
		str = s.hexString()
	default:
		// %s 和 %v 仅返回连续的非空白字符
		str = string(s.token(true, notSpace))
	}
	return
}

// 读取双引号或反引号字符串。
func (s *ss) quotedString() string {
	s.notEOF()
	quote := s.getRune()
	switch quote {
	case '`':
		// 读取直到遇到下一个反引号或 EOF
		for {
			r := s.mustReadRune()
			if r == quote {
				break
			}
			s.buf.WriteRune(r)
		}
		return string(s.buf)
	case '"':
		s.buf.WriteByte('"')
		for {
			r := s.mustReadRune()
			s.buf.WriteRune(r)
			if r == '\\' {
				// strconv.Unquote 会处理转义字符，这里只需要写入。
				s.buf.WriteRune(s.mustReadRune())
			} else if r == '"' {
				break
			}
		}
		result, err := strconv.Unquote(string(s.buf))
		if err != nil {
			s.error(err)
		}
		return result
	default:
		s.errorString("expected quoted string")
	}
	return ""
}

// hexDigit 返回十六进制字符所代表的十进制值
func hexDigit(d rune) (int, bool) {
	digit := int(d)
	switch digit {
	case '0', '1', '2', '3', '4', '5', '6', '7', '8', '9':
		return digit - '0', true
	case 'a', 'b', 'c', 'd', 'e', 'f':
		return 10 + digit - 'a', true
	case 'A', 'B', 'C', 'D', 'E', 'F':
		return 10 + digit - 'A', true
	}
	return -1, false
}

// 读取两个十六进制字符，并返回其所表示的字节。
// b ：读取的字节
// ok：是否读取成功
// 如果缺少后一个字符，则中止整个扫描过程，返回 err。
func (s *ss) hexByte() (b byte, ok bool) {
	// 处理第一个字符
	rune1 := s.getRune()
	if rune1 == eof {
		return
	}
	value1, ok := hexDigit(rune1)
	if !ok {
		s.UnreadRune()
		return
	}
	// 处理第二个字符
	value2, ok := hexDigit(s.mustReadRune())
	if !ok {
		s.errorString("illegal hex digit")
		return
	}
	// 转换为十进制数值
	return byte(value1<<4 | value2), true
}

// 读取十六进制字符串，并返回其所表示的内容
// 两个十六进制字符表示一个字节
// 读取失败则中止整个扫描过程，返回 err。
func (s *ss) hexString() string {
	s.notEOF()
	for {
		// 读取两个十六进制字符所表示的一个字节
		b, ok := s.hexByte()
		if !ok {
			break
		}
		s.buf.WriteByte(b)
	}
	if len(s.buf) == 0 {
		s.errorString("no hex data for %x string")
		return ""
	}
	return string(s.buf)
}

const (
	floatVerbs = "beEfFgGv"

	hugeWid = 1 << 30

	intBits     = 32 << (^uint(0) >> 63)
	uintptrBits = 32 << (^uintptr(0) >> 63)
)

// 处理一个 arg。
// 遇到错误则中止整个扫描过程，返回 err。
func (s *ss) scanOne(verb rune, arg interface{}) {
	s.buf = s.buf[:0]
	var err error
	// 如果参数有它自己的 Scan 方法，则调用它。
	if v, ok := arg.(Scanner); ok {
		err = v.Scan(s, verb)
		if err != nil {
			if err == io.EOF {
				err = io.ErrUnexpectedEOF
			}
			s.error(err)
		}
		return
	}

	// 根据不同的 arg 类型选择不同的解析方法。
	switch v := arg.(type) {
	case *bool:
		*v = s.scanBool(verb)
	case *complex64:
		*v = complex64(s.scanComplex(verb, 64))
	case *complex128:
		*v = s.scanComplex(verb, 128)
	case *int:
		*v = int(s.scanInt(verb, intBits))
	case *int8:
		*v = int8(s.scanInt(verb, 8))
	case *int16:
		*v = int16(s.scanInt(verb, 16))
	case *int32:
		*v = int32(s.scanInt(verb, 32))
	case *int64:
		*v = s.scanInt(verb, 64)
	case *uint:
		*v = uint(s.scanUint(verb, intBits))
	case *uint8:
		*v = uint8(s.scanUint(verb, 8))
	case *uint16:
		*v = uint16(s.scanUint(verb, 16))
	case *uint32:
		*v = uint32(s.scanUint(verb, 32))
	case *uint64:
		*v = s.scanUint(verb, 64)
	case *uintptr:
		*v = uintptr(s.scanUint(verb, uintptrBits))
	case *float32:
		if s.okVerb(verb, floatVerbs, "float32") {
			s.skipSpace(false)
			s.notEOF()
			*v = float32(s.convertFloat(s.floatToken(), 32))
		}
	case *float64:
		if s.okVerb(verb, floatVerbs, "float64") {
			s.skipSpace(false)
			s.notEOF()
			*v = s.convertFloat(s.floatToken(), 64)
		}
	case *string:
		*v = s.convertString(verb)
	case *[]byte:
		// 先扫描成字符串，然后再转换为 []byte，所以得到的是一个副本，
		// 如果我们扫描成 []byte，那么结果将指向缓冲区。
		*v = []byte(s.convertString(verb))
	default:
		val := reflect.ValueOf(v)
		ptr := val
		// arg 必须是一个指针，就像其它 arg 那样
		if ptr.Kind() != reflect.Ptr {
			s.errorString("type not a pointer: " + val.Type().String())
			return
		}
		// 接下来的流程和上面一样
		switch v := ptr.Elem(); v.Kind() {
		case reflect.Bool:
			v.SetBool(s.scanBool(verb))
		case reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64:
			v.SetInt(s.scanInt(verb, v.Type().Bits()))
		case reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64, reflect.Uintptr:
			v.SetUint(s.scanUint(verb, v.Type().Bits()))
		case reflect.String:
			v.SetString(s.convertString(verb))
		case reflect.Slice:
			typ := v.Type()
			// 对于切片，只能处理 []byte 的别名类型。
			if typ.Elem().Kind() != reflect.Uint8 {
				s.errorString("can't scan type: " + val.Type().String())
			}
			// 解析出字符串
			str := s.convertString(verb)
			// 转换为字节切片返回
			v.Set(reflect.MakeSlice(typ, len(str), len(str)))
			for i := 0; i < len(str); i++ {
				v.Index(i).SetUint(uint64(str[i]))
			}
		case reflect.Float32, reflect.Float64:
			s.skipSpace(false)
			s.notEOF()
			v.SetFloat(s.convertFloat(s.floatToken(), v.Type().Bits()))
		case reflect.Complex64, reflect.Complex128:
			v.SetComplex(s.scanComplex(verb, v.Type().Bits()))
		default:
			s.errorString("can't scan type: " + val.Type().String())
		}
	}
}

// 将本地引发的 panic（scanError 类型）和 EOF panic 转换为 error。
func errorHandler(errp *error) {
	if e := recover(); e != nil {
		// 本地 panic 转换为 error
		if se, ok := e.(scanError); ok {
			*errp = se.err
			// EOF panic 也转换为 error
		} else if eof, ok := e.(error); ok && eof == io.EOF {
			*errp = eof
		} else {
			panic(e)
		}
	}
}

// 扫描器的扫描过程
func (s *ss) doScan(a []interface{}) (numProcessed int, err error) {
	defer errorHandler(&err)

	// 循环处理所有 arg
	for _, arg := range a {
		s.scanOne('v', arg)
		numProcessed++
	}

	// 所有参数扫描结束

	// 检查是否以换行符或 EOF 结尾（Scanln 等需要这个错误信息）
	if s.nlIsEnd {
		for {
			r := s.getRune()
			if r == '\n' || r == eof {
				break
			}
			// 跳过空白字符后再次判断
			if !isSpace(r) {
				s.errorString("expected newline")
				break
			}
		}
	}
	return
}

// 处理非占位字符串，返回已处理的字节数。处理结果分为以下几种情况：
// 遇到占位符               ：返回 % 之前的字节数
// 不匹配                   ：返回 -1
// 完全匹配（format 被读完）：返回 len(foramt)
// 输入端被读完             ：强行中止扫描
// advance 的逻辑比较复杂，要配合 doScanf 理解，很难完全理解。
func (s *ss) advance(format string) (i int) {
	// 这里的 format 不是完整的格式字符串，而是由 doScanf 提供的
	// 未处理部分的格式字符串。doScanf 处理完一个占位符后，就把
	// 剩下的格式字符串交给 advance 处理。
	for i < len(format) {
		// 解码一个待处理字符
		fmtc, w := utf8.DecodeRuneInString(format[i:])

		// 1、处理遇到的 % 号

		if fmtc == '%' {
			// 不能以 % 结尾
			if i+w == len(format) {
				s.errorString("missing verb: % at end of format string")
			}
			nextc, _ := utf8.DecodeRuneInString(format[i+w:])
			// 遇到单独的 %（占位符）则返回 % 的下标 i（即 % 之前已处理的字节数）
			if nextc != '%' {
				return
			}
			// %% 被解析为一个 %，当做普通字符，交给后面处理
			i += w // 跳过 %% 中的前一个 %
		}

		// 2、处理 format 中的连续空白字符

		sawSpace := false   // 是否遇到连续的空白字符（包括换行符）
		wasNewline := false // 是否遇到换行符

		// 跳过连续的空白符
		for isSpace(fmtc) && i < len(format) {
			if fmtc == '\n' {
				if wasNewline {
					// 一次只处理一个换行符，之后的换行符交给后面处理
					break
				}
				// 登记遇到换行符
				wasNewline = true
			}
			// 登记遇到空白字符
			sawSpace = true
			i += w // 跳过已处理的空白字符
			// 更新待处理字符
			fmtc, w = utf8.DecodeRuneInString(format[i:])
		}

		// 到此，表示没有连续空白或已跳过连续空白，
		// 此时 i 指向非空白字符或换行符（即前面遇到的未处理的换行符）。

		// 3、对比输入端的连续空白字符

		if sawSpace {
			inputc := s.getRune()
			if inputc == eof {
				// 输入端被读空，返回已处理的字节数。
				// 返回后，在 doScanf 中继续判断 format 是否也被读完。
				return
			}
			// 输入端未遇到空白字符，匹配失败，中止整个扫描过程，返回 err。
			if !isSpace(inputc) {
				s.errorString("expected space in input to match format")
			}
			// 输入端也遇到空白字符，跳过空白部分。
			for inputc != '\n' && isSpace(inputc) {
				inputc = s.getRune()
			}
			// 此时 inputc 有可能为 eof
			// 输入端遇到换行符
			if inputc == '\n' {
				// format 中未遇到换行符，匹配失败，中止整个扫描过程，返回 err。
				if !wasNewline {
					s.errorString("newline in input does not match format")
				}

				// 到此，输入端和 format 中都遇到换行符，匹配成功。

				// 输入端换行符之后的空白没有继续处理，而 format 中却处理了，
				// 这将导致 "\n a %d" 无法匹配 "\n a 1"。使用的时候要注意。

				// 匹配完毕，返回已处理的字节数
				// 这里把 \n 当做一次扫描结束，这种行为类似于 Scanln。
				return
			}

			// 输入端空白字符处理完毕，未遇到换行符，则读取的应该是非空白字符。

			// 撤销对非空白字符的读取，交给下一轮去处理。
			// 如果之前读取的是 eof 则 UnreadRune 不会撤销任何内容。
			s.UnreadRune()
			// format 中遇到换行符，与输入端不匹配
			if wasNewline {
				s.errorString("newline in format does not match input")
			}
			// 空白部分（第二个换行符之前的）全部匹配成功，继续下一轮，处理后面的字符。
			continue
		}

		// 到此，表示 format 中没遇到空白字符或空白字符已经处理完毕。

		// 4、处理 format 中的非空白字符

		// 使用 mustReadRune 而不是 getRune 表示如果读取失败（EOF），
		// 则中止整个扫描过程，返回 err。
		inputc := s.mustReadRune()

		// 非空白字符匹配失败，撤销对 input 的读取，并返回 -1
		if fmtc != inputc {
			// 匹配失败，应该不需要再做什么了，不过 advance 作为一个独立的功能函数，
			// 还是要严谨一些，执行 s.UnreadRune 是为了保证输入端中已处理的内容与 
			// format 中 i 的位置对齐。
			s.UnreadRune()
			return -1
		}
		// 非空白字符匹配成功，继续处理下一个字符。
		i += w
	}
	// 全部处理完毕，返回 len(format)
	return
}

// 扫描器的格式化扫描过程
func (s *ss) doScanf(format string, a []interface{}) (numProcessed int, err error) {
	// 消化本地 panic，结束整个扫描过程。
	defer errorHandler(&err)
	end := len(format) - 1
	for i := 0; i <= end; {
		// 先处理 format 中的非占位符部分。
		w := s.advance(format[i:])
		// 循环直到遇到 % 字符
		if w > 0 {
			i += w
			continue
			// 这里有一个作用，就是当 i == len(format) 时，
			// 会终止循环，不会继续在后面访问 format[i]
		}

		// 到这里，表示 format 没有处理完，而且应该处理占位符了。

		// 没有遇找到占位符，看看是什么原因
		if format[i] != '%' {
			// 非空白字符匹配失败
			if w < 0 {
				s.errorString("input does not match format")
			}
			// 到此，表示遇到 EOF

			// 不过代码不会执行到这里，因为在 advance 中 EOF 会引发 panic
			// 为了逻辑的严谨，这里还是需要添加一个 break，以防 advance 发生改变。
			break
		}

		i++ // 跳过 % 号

		// 读取占位符中的宽度信息
		var widPresent bool
		s.maxWid, widPresent, i = parsenum(format, i, end)
		// 如果没有设置宽度信息，则将宽度设置为默认值 hugeWid
		if !widPresent {
			s.maxWid = hugeWid // hugeWid 是常量 1 << 30
		}
		// 获取动词
		c, w := utf8.DecodeRuneInString(format[i:])
		i += w // 跳过动词

		// 如果动词不是 c，则跳过输入端开头的空白
		if c != 'c' {
			s.SkipSpace()
		}

		// 默认读取限制
		s.argLimit = s.limit
		// 根据占位符中的宽度信息设置输入端允许读出的最大字符数
		if f := s.count + s.maxWid; f < s.argLimit {
			s.argLimit = f
		}

		// arg 太少，占位符太多，数量不匹配。
		if numProcessed >= len(a) {
			s.errorString("too few operands for format '%" + format[i-w:] + "'")
			break
		}
		arg := a[numProcessed]

		s.scanOne(c, arg) // 处理一个 arg
		numProcessed++    // 跳过已处理的 arg
		// 恢复默认读取限制
		s.argLimit = s.limit
	}
	// arg 太多，占位符太少，数量不匹配。
	if numProcessed < len(a) {
		s.errorString("too many operands")
	}
	return
}
```