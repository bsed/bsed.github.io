---
title: Golang 学习 - utf8.go
date: 2016-07-20 16:37:00
updated: 2017-02-21 16:38:13
tags: 
- cpp
- emacs
categories: 
- linux

---
```golang
// Copyright 2009 The Go Authors. All rights reserved.
// Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

// go/src/unicode/utf8/utf8.go
// version 1.7

package utf8


<!--more-->


// 编码所需的基本数字
const (
	RuneError = '\uFFFD'     // 错误的 Rune 或 Unicode 代理字符
	RuneSelf  = 0x80         // ASCII 字符范围
	MaxRune   = '\U0010FFFF' // Unicode 码点的最大值
	UTFMax    = 4            // 一个字符编码的最大长度
)

// Unicode 代理字符对 UTF-8 编码而言是无效的。
const (
	surrogateMin = 0xD800
	surrogateMax = 0xDFFF
)

// 用词说明：
// 单字节字符：该字符的 UTF-8 编码需要一个字节存放
// 双字节字符：该字符的 UTF-8 编码需要两个字节存放
// 三字节字符：该字符的 UTF-8 编码需要三个字节存放
// 四字节字符：该字符的 UTF-8 编码需要四个字节存放
// 字符首字节：某字符的 UTF-8 编码中的第一个字节
// 字符次字节：某字符的 UTF-8 编码中的第二个字节
// 字符后续字节：某字符的 UTF-8 编码中首字节后面的其它字节

const (
	// 位标记（用于判断字节有效性）
	t1 = 0x00 // 0000 0000 单字节字符的首字节标记（二进制以 0     开头）
	tx = 0x80 // 1000 0000 所有字符的后续字节标记（二进制以 10    开头）
	t2 = 0xC0 // 1100 0000 双字节字符的首字节标记（二进制以 110   开头）
	t3 = 0xE0 // 1110 0000 三字节字符的首字节标记（二进制以 1110  开头）
	t4 = 0xF0 // 1111 0000 四字节字符的首字节标记（二进制以 11110 开头）
	t5 = 0xF8 // 1111 1000 好像未使用

	// 位掩码（用于获取标记之外的二进制位）
	maskx = 0x3F // 0011 1111 所有字符的后续字节掩码
	mask2 = 0x1F // 0001 1111 双字节字符的首字节掩码
	mask3 = 0x0F // 0000 1111 三字节字符的首字节掩码
	mask4 = 0x07 // 0000 0111 四字节字符的首字节掩码

	rune1Max = 1<<7 - 1  // 单字节字符的总数（127   个）
	rune2Max = 1<<11 - 1 // 双字节字符的总数（2047  个）
	rune3Max = 1<<16 - 1 // 三字节字符的总数（65535 个）

	// UTF-8 字符的后续字节的一般取值范围
	locb = 0x80 // 1000 0000
	hicb = 0xBF // 1011 1111

	// 字符首字节分类标记，用于将所有的字符首字节分成下面九类，分别处理。
	// 以下十六进制常量的高位和低位分别表示不同的含义：
	// 高位：“次字节取值范围列表”的索引，如果高位是 F 则表示字符是单字节字符
	// 低位：字符的编码长度，如果高位是 F 则低位表示单字节字符的状态：有效、无效
	xx = 0xF1 // 无索引，长度 1，对应无效 UTF-8 编码
	as = 0xF0 // 无索引，长度 1，对应普通 ASCII 字符
	s1 = 0x02 // 索引 0, 长度 2，对应普通“双字节字符”的首字节
	s2 = 0x13 // 索引 1, 长度 3，对应特殊“双字节字符”的首字节 0xE0（用于编码长度跨越）
	s3 = 0x03 // 索引 0, 长度 3，对应普通“三字节字符”的首字节
	s4 = 0x23 // 索引 2, 长度 3，对应特殊“三字节字符”的首字节 0xED（用于代理区检测）
	s5 = 0x34 // 索引 3, 长度 4，对应特殊“四字节字符”的首字节 0xF0（用于编码长度跨越）
	s6 = 0x04 // 索引 0, 长度 4，对应普通“四字节字符”的首字节
	s7 = 0x44 // 索引 4, 长度 4，对应特殊“四字节字符”的首字节 0xF4（用于范围检测）
)

// first 是关于 UTF-8 字符中首字节的编码信息。
// 将所有的首字节进行分类，分为：xx、as、s1、s2、s3、s4、s5、s6、s7 九类，
// 其中 xx 代表无效首字节，s1 代表双字节字符的首字节
// s2、s3、s4 代表三字节字符的首字节
// s5、s6、a7 代表四字节字符的首字节
var first = [256]uint8{
	//   1   2   3   4   5   6   7   8   9   A   B   C   D   E   F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x00-0x0F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x10-0x1F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x20-0x2F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x30-0x3F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x40-0x4F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x50-0x5F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x60-0x6F
	as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, as, // 0x70-0x7F
	//   1   2   3   4   5   6   7   8   9   A   B   C   D   E   F
	xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, // 0x80-0x8F
	xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, // 0x90-0x9F
	xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, // 0xA0-0xAF
	xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, // 0xB0-0xBF
	xx, xx, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, // 0xC0-0xCF
	s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, s1, // 0xD0-0xDF
	s2, s3, s3, s3, s3, s3, s3, s3, s3, s3, s3, s3, s3, s4, s3, s3, // 0xE0-0xEF
	s5, s6, s6, s6, s7, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, xx, // 0xF0-0xFF
}

// acceptRange 给出次字节的取值范围。
type acceptRange struct {
	lo uint8 // 次字节最小取值
	hi uint8 // 次字节最大取值
}

// 不同的首字节字符有不同的次字节取值范围，
// UTF-8 编码编不出这些范围之外的次字节内容。
var acceptRanges = [...]acceptRange{
	// 普通字符的次字节，范围之外为无效编码（即二进制位不是以 10 开头）
	0: {locb, hicb},
	// 三字节特殊字符（首字节为 0xE0）的次字节：
	// 如果次字节低于 0xA0 则该字符应该用两个字节表示，而不是三个字节。
	// 如果次字节高于 hicb 则该字节为无效编码（即二进制位不是以 10 开头）
	1: {0xA0, hicb},
	// 三字节特殊字符（首字节为 0xED）的次字节：
	// 如果次字节低于 locb 则该字节为无效编码（即二进制位不是以 10 开头）
	// 如果次字节高于 0x9F 则该字符为代理区字符（[ED A0 80] - [ED BF BF]）
	2: {locb, 0x9F},
	// 四字节特殊字符（首字节为 0xF0）的次字节：
	// 如果次字节低于 0x90 则该字符应该用三个字节表示，而不是四个字节。
	// 如果次字节高于 hicb 则该字节为无效编码（即二进制位不是以 10 开头）
	3: {0x90, hicb},
	// 四字节特殊字符（首字节为 0xF4）的次字节：
	// 如果次字节低于 locb 则该字节为无效编码（即二进制位不是以 10 开头）
	// 如果次字节高于 0x8F 则该字符超出 Unicode 范围（超出 MaxRune）
	4: {locb, 0x8F},

	// 相邻字符的编码长度跨越：

	// [11011111 10111111]          [DF BF]    // U+07FF 的后一个字符为三字节
	// [11100000 10100000 10000000] [E0 A0 80] // U+0800 的前一个字符为两字节

	// [11101111 10111111 10111111]          [EF BF BF]    // U+FFFF  的后一个字符为四字节
	// [11110000 10010000 10000000 10000000] [F0 90 80 80] // U+10000 的前一个字符为三字节
}

// FullRune 判断 p 是否以一个完整（但不一定有效）的 UTF-8 字符开头。
// 一个无效的编码也被认为是完整字符，因为它将被转换为一个 RuneError 字符。
// 只有“编码有效但长度不够”的字符才被认为是不完整字符。
// 也就是说，只有截去一个有效字符的一个或多个后续字节，该字符才算是不完整字符。
// 举例：
// "好"     是完整字符
// "好"[1:] 是完整字符（首字节无效，转换为 RuneError 字符）
// "好"[2:] 是完整字符（首字节无效，转换为 RuneError 字符）
// "好"[:2] 是不完整字符（编码有效但长度不够）
// "好"[:1] 是不完整字符（编码有效但长度不够）
func FullRune(p []byte) bool {
	n := len(p)
	if n == 0 {
		return false
	}
	// 查表并计算，获取编码长度，判断 p 的长度是否满足编码长度
	x := first[p[0]]
	if n >= int(x&7) { // x&7 获取的就是编码长度
		// p 的长度满足编码长度，表示 p 是一个完整的字符开头。
		return true
	}

	// 此时 p 的长度不够，应该是不完整的字符了，但是如果 p 中是无效编码，也算完整。
	// 此时 n 肯定小于 4，否则长度不可能不够。

	// 获取首字节对应的次字节有效范围
	accept := acceptRanges[x>>4]
	if n > 1 {
		if c := p[1]; c < accept.lo || accept.hi < c {
			// 有一个无效字节，算完整字符
			return true
		} else if n > 2 && (p[2] < locb || hicb < p[2]) {
			// 有一个无效字节，算完整字符
			return true
		}
	}
	// 全是有效字节，但长度不够，算不完整
	return false
}

// 功能同 FullRune，只不过参数为字符串。
func FullRuneInString(s string) bool {
	n := len(s)
	if n == 0 {
		return false
	}
	x := first[s[0]]
	if n >= int(x&7) {
		return true
	}
	accept := acceptRanges[x>>4]
	if n > 1 {
		if c := s[1]; c < accept.lo || accept.hi < c {
			return true
		} else if n > 2 && (s[2] < locb || hicb < s[2]) {
			return true
		}
	}
	return false
}

// 解码 UTF-8 序列 p 中的第一个 Unicode 字符。
// r   ：解码出的字符
// size：该字符的 UTF-8 编码长度
// 如果 p 为空，则返回 RuneError, 0
// 如果 p 为无效的 UTF-8 编码，则返回 RuneError, 1
// 无效 UTF-8 编码：UTF-8 编码不正确（比如长度不够）、结果超出 Unicode 范围、
// 编码不是最短的。
// 可以用四个字节编码一个单字节字符，但它不是最短的，比如：
// [111100000 10000000 10000000 10111000] 不是最短的，应该使用 [00111000]
func DecodeRune(p []byte) (r rune, size int) {
	n := len(p)
	if n < 1 {
		return RuneError, 0
	}
	// 处理单字节字符
	p0 := p[0]
	x := first[p0]
	if x >= as { // x 为 F0 或 F1
		// 生成 0x0000 或 0xFFFF
		mask := rune(x) << 31 >> 31
		// return 保留 ASCII 字符 | 保留 RuneError, 1
		return rune(p[0])&^mask | RuneError&mask, 1
	}

	// 处理多字节字符

	// 获取编码长度
	sz := x & 7
	// 获取次字节有效范围
	accept := acceptRanges[x>>4]
	// p 长度不够
	if n < int(sz) {
		return RuneError, 1
	}

	// p 长度满足

	// 次字节编码有效
	b1 := p[1]
	if b1 < accept.lo || accept.hi < b1 {
		return RuneError, 1
	}
	// 处理有效的双字节字符
	if sz == 2 {
		return rune(p0&mask2)<<6 | rune(b1&maskx), 2
	}
	// 超过双字节，第三字节编码有效
	b2 := p[2]
	if b2 < locb || hicb < b2 {
		return RuneError, 1
	}
	// 处理有效的三字节字符
	if sz == 3 {
		return rune(p0&mask3)<<12 | rune(b1&maskx)<<6 | rune(b2&maskx), 3
	}
	// 超过三字节，第四字节编码有效
	b3 := p[3]
	if b3 < locb || hicb < b3 {
		return RuneError, 1
	}
	// 处理有效的四字节字符
	return rune(p0&mask4)<<18 | rune(b1&maskx)<<12 | rune(b2&maskx)<<6 | rune(b3&maskx), 4
}

// 功能同 DecodeRune，只不过参数为字符串
func DecodeRuneInString(s string) (r rune, size int) {
	n := len(s)
	if n < 1 {
		return RuneError, 0
	}
	s0 := s[0]
	x := first[s0]
	if x >= as {
		mask := rune(x) << 31 >> 31
		return rune(s[0])&^mask | RuneError&mask, 1
	}
	sz := x & 7
	accept := acceptRanges[x>>4]
	if n < int(sz) {
		return RuneError, 1
	}
	s1 := s[1]
	if s1 < accept.lo || accept.hi < s1 {
		return RuneError, 1
	}
	if sz == 2 {
		return rune(s0&mask2)<<6 | rune(s1&maskx), 2
	}
	s2 := s[2]
	if s2 < locb || hicb < s2 {
		return RuneError, 1
	}
	if sz == 3 {
		return rune(s0&mask3)<<12 | rune(s1&maskx)<<6 | rune(s2&maskx), 3
	}
	s3 := s[3]
	if s3 < locb || hicb < s3 {
		return RuneError, 1
	}
	return rune(s0&mask4)<<18 | rune(s1&maskx)<<12 | rune(s2&maskx)<<6 | rune(s3&maskx), 4
}

// 功能同 DecodeRune，只不过解码的是最后一个字符。
func DecodeLastRune(p []byte) (r rune, size int) {
	end := len(p)
	if end == 0 {
		return RuneError, 0
	}
	// 处理 p 的最后一个字节
	start := end - 1
	r = rune(p[start])
	if r < RuneSelf { // 单字节字符直接返回
		return r, 1
	}
	// 一次最多遍历 4 个字节，避免因无效 UTF8 编码造成的过度循环
	lim := end - UTFMax
	if lim < 0 {
		lim = 0
	}
	// 按字节反向遍历
	for start--; start >= lim; start-- {
		if RuneStart(p[start]) { // 遇到首字节编码即可
			break
		}
	}
	// 遍历完了也没遇到首字节，则解码整个 p
	if start < 0 {
		start = 0
	}
	r, size = DecodeRune(p[start:end])
	// 遇到无效编码，则只将最后一个字节解码为 RuneError
	if start+size != end {
		return RuneError, 1
	}
	// 解码成功
	return r, size
}

// 功能同 DecodeLastRune，只不过参数为字符串
func DecodeLastRuneInString(s string) (r rune, size int) {
	end := len(s)
	if end == 0 {
		return RuneError, 0
	}
	start := end - 1
	r = rune(s[start])
	if r < RuneSelf {
		return r, 1
	}
	lim := end - UTFMax
	if lim < 0 {
		lim = 0
	}
	for start--; start >= lim; start-- {
		if RuneStart(s[start]) {
			break
		}
	}
	if start < 0 {
		start = 0
	}
	r, size = DecodeRuneInString(s[start:end])
	if start+size != end {
		return RuneError, 1
	}
	return r, size
}

// RuneLen 返回 r 的 UTF-8 编码所占用的字节数。
// 如果 r 不是一个有效的值（代理区或超出范围），则返回 -1。
func RuneLen(r rune) int {
	switch {
	case r < 0: // 超出范围
		return -1
	case r <= rune1Max: // 单字节字符范围
		return 1
	case r <= rune2Max: // 双字节字符范围
		return 2
	case surrogateMin <= r && r <= surrogateMax: // 代理区范围
		return -1
	case r <= rune3Max: // 三字节字符范围
		return 3
	case r <= MaxRune: // 四字节字符范围
		return 4
	}
	return -1 // 超出范围
}

// EncodeRune 将 r 编码为 UTF-8 序列，结果写入 p 中（p 必须足够长，一般为 4）
// 返回写入的字节数
func EncodeRune(p []byte, r rune) int {
	// 负数是错误的，将其转换为无符号数，以使其超出范围，进而处理掉这个错误。
	switch i := uint32(r); {
	case i <= rune1Max: // 单字节字符
		p[0] = byte(r)
		return 1
	case i <= rune2Max: // 双字节字符
		p[0] = t2 | byte(r>>6)
		p[1] = tx | byte(r)&maskx
		return 2
	// 超出范围或代理区字符
	case i > MaxRune, surrogateMin <= i && i <= surrogateMax:
		r = RuneError
		fallthrough
	case i <= rune3Max: // 三字节字符
		p[0] = t3 | byte(r>>12)
		p[1] = tx | byte(r>>6)&maskx
		p[2] = tx | byte(r)&maskx
		return 3
	default: // 四字节字符
		p[0] = t4 | byte(r>>18)
		p[1] = tx | byte(r>>12)&maskx
		p[2] = tx | byte(r>>6)&maskx
		p[3] = tx | byte(r)&maskx
		return 4
	}
}

// RuneCount 返回 p 中的字符数（不是字节数）
// 错误的和长度无效的编码中的每一个字节都会被当做一个字符处理。
// RuneError 被视为一个字符
func RuneCount(p []byte) int {
	np := len(p)
	var n int
	for i := 0; i < np; {
		n++
		c := p[i]
		if c < RuneSelf {
			i++ // 单字节字符
			continue
		}
		// 查表判断首字节的有效性
		x := first[c]
		if x == xx {
			i++ // 首字节无效，字节当做一个字符处理
			continue
		}
		// 首字节有效
		size := int(x & 7)
		if i+size > np {
			i++ // 但长度不足，字节当做一个字符处理
			continue
		}
		// 首字节有效，长度也够，判断后续字节的有效性
		accept := acceptRanges[x>>4]
		if c := p[i+1]; c < accept.lo || accept.hi < c { // 次字节无效
			size = 1
		} else if size == 2 { // 次字节有效，长度刚好为 2
		} else if c := p[i+2]; c < locb || hicb < c { // 第三字节无效
			size = 1
		} else if size == 3 { // 第三字节也有效，长度刚好为 3
		} else if c := p[i+3]; c < locb || hicb < c { // 第四字节无效
			size = 1
		} // 第四字节也有效，长度不是 1、2、3，肯定为 4（size == 4）
		i += size
	}
	return n
}

// 功能同 RuneCount，只不过参数为字符串
func RuneCountInString(s string) (n int) {
	ns := len(s)
	for i := 0; i < ns; n++ {
		c := s[i]
		if c < RuneSelf {
			i++
			continue
		}
		x := first[c]
		if x == xx {
			i++
			continue
		}
		size := int(x & 7)
		if i+size > ns {
			i++
			continue
		}
		accept := acceptRanges[x>>4]
		if c := s[i+1]; c < accept.lo || accept.hi < c {
			size = 1
		} else if size == 2 {
		} else if c := s[i+2]; c < locb || hicb < c {
			size = 1
		} else if size == 3 {
		} else if c := s[i+3]; c < locb || hicb < c {
			size = 1
		}
		i += size
	}
	return n
}

// RuneStart 判断 b 是否为 UTF-8 字符编码的首字节（有可能是无效字节）。
// UTF-8 编码的后续字节的二进制位都是以 10 开始的。
func RuneStart(b byte) bool { return b&0xC0 != 0x80 }

// Valid 判断 p 是否完全由有效的 UTF-8 编码组成。
func Valid(p []byte) bool {
	// 代码同 RuneCount 类似
	n := len(p)
	for i := 0; i < n; {
		pi := p[i]
		if pi < RuneSelf {
			i++
			continue
		}
		x := first[pi]
		if x == xx {
			return false
		}
		size := int(x & 7)
		if i+size > n {
			return false
		}
		accept := acceptRanges[x>>4]
		if c := p[i+1]; c < accept.lo || accept.hi < c {
			return false
		} else if size == 2 {
		} else if c := p[i+2]; c < locb || hicb < c {
			return false
		} else if size == 3 {
		} else if c := p[i+3]; c < locb || hicb < c {
			return false
		}
		i += size
	}
	return true
}

// 功能同 Valid，只不过参数为字符串
func ValidString(s string) bool {
	n := len(s)
	for i := 0; i < n; {
		si := s[i]
		if si < RuneSelf {
			i++
			continue
		}
		x := first[si]
		if x == xx {
			return false
		}
		size := int(x & 7)
		if i+size > n {
			return false
		}
		accept := acceptRanges[x>>4]
		if c := s[i+1]; c < accept.lo || accept.hi < c {
			return false
		} else if size == 2 {
		} else if c := s[i+2]; c < locb || hicb < c {
			return false
		} else if size == 3 {
		} else if c := s[i+3]; c < locb || hicb < c {
			return false
		}
		i += size
	}
	return true
}

// ValidRune 判断 r 是否可以被编码成 UTF-8 序列。
// 代理区字符或超出范围则返回 false。
func ValidRune(r rune) bool {
	switch {
	case r < 0: // 超出范围
		return false
	case surrogateMin <= r && r <= surrogateMax: // 代理区字符
		return false
	case r > MaxRune: // 超出范围
		return false
	}
	return true
}
```