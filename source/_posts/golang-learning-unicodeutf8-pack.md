---
title: Golang学习 - unicode/utf8 包
date: 2016-04-22 17:18:00
updated: 2017-02-20 17:27:33
tags: 
- golang
- sync
categories: 
- go

---
```golang
------------------------------------------------------------

// 编码所需的基本数字
const (
	RuneError = '\uFFFD'     // 错误的 Rune 或 Unicode 代理字符
	RuneSelf  = 0x80         // ASCII 字符范围
	MaxRune   = '\U0010FFFF' // Unicode 码点的最大值
	UTFMax    = 4            // 一个字符编码的最大长度
)


<!--more-->


// 将 r 转换为 UTF-8 编码写入 p 中（p 必须足够长，通常为 4 个字节）
// 如果 r 是无效的 Unicode 字符，则写入 RuneError
// 返回写入的字节数
func EncodeRune(p []byte, r rune) int

// 解码 p 中的第一个字符，返回解码后的字符和 p 中被解码的字节数
// 如果 p 为空，则返回（RuneError, 0）
// 如果 p 中的编码无效，则返回（RuneError, 1）
// 无效编码：UTF-8 编码不正确（比如长度不够）、结果超出 Unicode 范围、编码不是最短的。
// 关于最短编码：可以用四个字节编码一个单字节字符，但它不是最短的，比如：
// [111100000 10000000 10000000 10111000] 不是最短的，应该使用 [00111000]
func DecodeRune(p []byte) (r rune, size int)

// 功能同上，参数为字符串
func DecodeRuneInString(s string) (r rune, size int)

// 解码 p 中的最后一个字符，返回解码后的字符，和 p 中被解码的字节数
// 如果 p 为空，则返回（RuneError, 0）
// 如果 p 中的编码无效，则返回（RuneError, 1）
func DecodeLastRune(p []byte) (r rune, size int)

// 功能同上，参数为字符串
func DecodeLastRuneInString(s string) (r rune, size int)

// FullRune 检测 p 中第一个字符的 UTF-8 编码是否完整（完整并不表示有效）。
// 一个无效的编码也被认为是完整字符，因为它将被转换为一个 RuneError 字符。
// 只有“编码有效但长度不够”的字符才被认为是不完整字符。
// 也就是说，只有截去一个有效字符的一个或多个尾部字节，该字符才算是不完整字符。
// 举例：
// "好"     是完整字符
// "好"[1:] 是完整字符（首字节无效，可转换为 RuneError 字符）
// "好"[2:] 是完整字符（首字节无效，可转换为 RuneError 字符）
// "好"[:2] 是不完整字符（编码有效但长度不够）
// "好"[:1] 是不完整字符（编码有效但长度不够）
func FullRune(p []byte) bool

// 功能同上，参数为字符串
func FullRuneInString(s string) bool

// 返回 p 中的字符个数
// 错误的 UTF8 编码和长度不足的 UTF8 编码将被当作单字节的 RuneError 处理
func RuneCount(p []byte) int

// 功能同上，参数为字符串
func RuneCountInString(s string) (n int)

// RuneLen 返回需要多少字节来编码字符 r，如果 r 是无效的字符，则返回 -1
func RuneLen(r rune) int

// 判断 b 是否为 UTF8 字符的首字节编码，最高位(bit)是不是 10 的字节就是首字节。
func RuneStart(b byte) bool

// Valid 判断 p 是否为完整有效的 UTF8 编码序列。
func Valid(p []byte) bool

// 功能同上，参数为字符串
func ValidString(s string) bool

// ValidRune 判断 r 能否被正确的转换为 UTF8 编码
// 超出 Unicode 范围的码点或 UTF-16 代理区中的码点是不能转换的
func ValidRune(r rune) bool

------------------------------

// 示例
func main() {
	b := make([]byte, utf8.UTFMax)

	n := utf8.EncodeRune(b, '好')
	fmt.Printf("%v：%v\n", b, n) // [229 165 189 0]：3

	r, n := utf8.DecodeRune(b)
	fmt.Printf("%c：%v\n", r, n) // 好：3

	s := "大家好"
	for i := 0; i < len(s); {
		r, n = utf8.DecodeRuneInString(s[i:])
		fmt.Printf("%c：%v   ", r, n) // 大：3   家：3   好：3
		i += n
	}
	fmt.Println()

	for i := len(s); i > 0; {
		r, n = utf8.DecodeLastRuneInString(s[:i])
		fmt.Printf("%c：%v   ", r, n) // 好：3   家：3   大：3
		i -= n
	}
	fmt.Println()

	b = []byte("好")
	fmt.Printf("%t, ", utf8.FullRune(b))     // true
	fmt.Printf("%t, ", utf8.FullRune(b[1:])) // true
	fmt.Printf("%t, ", utf8.FullRune(b[2:])) // true
	fmt.Printf("%t, ", utf8.FullRune(b[:2])) // false
	fmt.Printf("%t\n", utf8.FullRune(b[:1])) // false

	b = []byte("大家好")
	fmt.Println(utf8.RuneCount(b)) // 3

	fmt.Printf("%d, ", utf8.RuneLen('A'))          // 1
	fmt.Printf("%d, ", utf8.RuneLen('\u03A6'))     // 2
	fmt.Printf("%d, ", utf8.RuneLen('好'))          // 3
	fmt.Printf("%d, ", utf8.RuneLen('\U0010FFFF')) // 4
	fmt.Printf("%d\n", utf8.RuneLen(0x1FFFFFFF))   // -1

	fmt.Printf("%t, ", utf8.RuneStart("好"[0])) // true
	fmt.Printf("%t, ", utf8.RuneStart("好"[1])) // false
	fmt.Printf("%t\n", utf8.RuneStart("好"[2])) // false

	b = []byte("你好")
	fmt.Printf("%t, ", utf8.Valid(b))     // true
	fmt.Printf("%t, ", utf8.Valid(b[1:])) // false
	fmt.Printf("%t, ", utf8.Valid(b[2:])) // false
	fmt.Printf("%t, ", utf8.Valid(b[:2])) // false
	fmt.Printf("%t, ", utf8.Valid(b[:1])) // false
	fmt.Printf("%t\n", utf8.Valid(b[3:])) // true

	fmt.Printf("%t, ", utf8.ValidRune('好'))        // true
	fmt.Printf("%t, ", utf8.ValidRune(0))          // true
	fmt.Printf("%t, ", utf8.ValidRune(0xD800))     // false  代理区字符
	fmt.Printf("%t\n", utf8.ValidRune(0x10FFFFFF)) // false  超出范围
}

```