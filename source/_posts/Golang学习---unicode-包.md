---
title: "Golang学习 - unicode 包"
categories: [ "Golang" ]
tags: [ "golang","unicode" ]
draft: false
slug: "golang-learning-unicode-pack"
date: "2016-04-21 17:39:00"
---

```golang
const (
	MaxRune         = '\U0010FFFF' // Unicode 码点的最大值
	ReplacementChar = '\uFFFD'     // 表示无效的码点
	MaxASCII        = '\u007F'     // 最大 ASCII 值
	MaxLatin1       = '\u00FF'     // 最大 Latin-1 值
)

<!--more-->


// 判断字符 r 是否在 rangtab 范围内。
// 可用的 RangeTable 参见 go/src/unicode/tables.go。
func Is(rangeTab *RangeTable, r rune) bool

// RangeTable 定义一个 Unicode 码点集合，包含 16 位和 32 位两个范围列表。
// 这两个列表必须经过排序而且不能重叠。R32 中只能包含大于 16 位的值。
type RangeTable struct {
	R16         []Range16
	R32         []Range32
	LatinOffset int // R16 中 Hi <= MaxLatin1 的条目数。
}

// Range16 表示一个 16 位的 Unicode 码点范围。范围从 Lo 到 Hi，具有指定的步长。
type Range16 struct {
	Lo     uint16
	Hi     uint16
	Stride uint16 // 步长
}

// Range32 表示一个 32 位的 Unicode 码点范围。范围从 Lo 到 Hi，具有指定的步长。
// Lo 和 Hi 必须都大于 16 位。
type Range32 struct {
	Lo     uint32
	Hi     uint32
	Stride uint32 // 步长
}

------------------------------

// 判断字符 r 是否为大写格式
func IsUpper(r rune) bool

// 判断字符 r 是否为小写格式
func IsLower(r rune) bool

// 判断字符 r 是否为 Unicode 规定的 Title 字符
// 大部分字符的 Title 格式就是其大写格式
// 只有少数字符的 Title 格式是特殊字符
// 这里判断的就是特殊字符
func IsTitle(r rune) bool

// ToUpper 将字符 r 转换为大写格式
func ToUpper(r rune) rune

// ToLower 将字符 r 转换为小写格式
func ToLower(r rune) rune

// ToTitle 将字符 r 转换为 Title 格式
// 大部分字符的 Title 格式就是其大写格式
// 只有少数字符的 Title 格式是特殊字符
func ToTitle(r rune) rune

// To 将字符 r 转换为指定的格式
// _case 取值：UpperCase、LowerCase、TitleCase
func To(_case int, r rune) rune

------------------------------

// 示例：判断汉字
func main() {
	for _, r := range "Hello 世界！" {
		// 判断字符是否为汉字
		if unicode.Is(unicode.Scripts["Han"], r) {
			fmt.Printf("%c", r) // 世界
		}
	}
}

------------------------------

// 示例：判断大小写
func main() {
	for _, r := range "Hello ＡＢＣ！" {
		// 判断字符是否为大写
		if unicode.IsUpper(r) {
			fmt.Printf("%c", r) // HＡＢＣ
		}
	}
	for _, r := range "Hello ａｂｃ！" {
		// 判断字符是否为小写
		if unicode.IsLower(r) {
			fmt.Printf("%c", r) // elloａｂｃ
		}
	}
	for _, r := range "Hello ᾏᾟᾯ！" {
		// 判断字符是否为标题
		if unicode.IsTitle(r) {
			fmt.Printf("%c", r) // ᾏᾟᾯ
		}
	}
}

------------------------------

// 示例：输出 Unicode 规定的标题字符
func main() {
	for _, cr := range unicode.Lt.R16 {
		for i := cr.Lo; i <= cr.Hi; i += cr.Stride {
			fmt.Printf("%c", i)
		}
	} // ǅǈǋǲᾈᾉᾊᾋᾌᾍᾎᾏᾘᾙᾚᾛᾜᾝᾞᾟᾨᾩᾪᾫᾬᾭᾮᾯᾼῌῼ
}

------------------------------

// 示例：转换大小写
func main() {
	s := "Hello 世界！"

	for _, r := range s {
		fmt.Printf("%c", unicode.ToUpper(r))
	} // HELLO 世界！
	for _, r := range s {
		fmt.Printf("%c", unicode.ToLower(r))
	} // hello 世界！
	for _, r := range s {
		fmt.Printf("%c", unicode.ToTitle(r))
	} // HELLO 世界！

	for _, r := range s {
		fmt.Printf("%c", unicode.To(unicode.UpperCase, r))
	} // HELLO 世界！
	for _, r := range s {
		fmt.Printf("%c", unicode.To(unicode.LowerCase, r))
	} // hello 世界！
	for _, r := range s {
		fmt.Printf("%c", unicode.To(unicode.TitleCase, r))
	} // HELLO 世界！
}

------------------------------------------------------------

// ToUpper 将 r 转换为大写格式
// 优先使用指定的映射表 special
// 可用的 SpecialCase 参见 go/src/unicode/casetables.go。
func (special SpecialCase) ToUpper(r rune) rune

// ToLower 将 r 转换为小写格式
// 优先使用指定的映射表 special
func (special SpecialCase) ToLower(r rune) rune

// ToTitle 将 r 转换为 Title 格式
// 优先使用指定的映射表 special
func (special SpecialCase) ToTitle(r rune) rune

// SpecialCase 表示特定语言的大小写映射，比如土耳其语。
// SpecialCase 的方法可以自定义标准映射（通过重写）。
type SpecialCase []CaseRange

// CaseRange 表示一个简单的 Unicode 码点范围，用于大小写转换。
// 在 Lo 和 Hi 范围内的码点，如果要转换成大写，只需要加上 d[0] 即可
// 如果要转换为小写，只需要加上 d[1] 即可，如果要转换为 Title 格式，
// 只需要加上 d[2] 即可。
type CaseRange struct {
	Lo    uint32
	Hi    uint32
	Delta d // [3]rune
}

// CaseRanges 中 Delta 数组的索引。
const (
	UpperCase = iota
	LowerCase
	TitleCase
	MaxCase
)

// 如果一个 CaseRange 中的 Delta 元素是 UpperLower，则表示这个 CaseRange 是
// 一个有着连续的大写小写大写小写的范围。也就是说，Lo 是大写，Lo+1 是小写，
// Lo+2 是大写，Lo+3 是小写 ... 一直到 Hi 为止。
const (
	UpperLower = MaxRune + 1 // 不是一个有效的 Delta 元素
)

------------------------------

// 示例
func main() {
	s := "Hello 世界！"
	for _, r := range s {
		fmt.Printf("%c", unicode.SpecialCase(unicode.CaseRanges).ToUpper(r))
	} // HELLO 世界！
	for _, r := range s {
		fmt.Printf("%c", unicode.SpecialCase(unicode.CaseRanges).ToLower(r))
	} // hello 世界！
	for _, r := range s {
		fmt.Printf("%c", unicode.SpecialCase(unicode.CaseRanges).ToTitle(r))
	} // HELLO 世界！
}

------------------------------------------------------------

// SimpleFold 在 Unicode 字符表中从字符 r 开始环绕查找（到尾部后再从头开始）
// 下一个与 r 大小写相匹配的字符（一个字符的大写、小写、标题三者视为大小写相
// 匹配），这个函数遵循 Unicode 定义的大小写环绕匹配表。
//
// 例如：
// SimpleFold('A') = 'a'
// SimpleFold('a') = 'A'
//
// SimpleFold('K') = 'k'
// SimpleFold('k') = 'K' (开尔文符号)
// SimpleFold('K') = 'K'
//
// SimpleFold('1') = '1'
func SimpleFold(r rune) rune

------------------------------

// 示例：SimpleFold
func main() {
	s := "ΦφϕkKK"
	// 看看 s 里面是什么
	for _, c := range s {
		fmt.Printf("%x  ", c)
	}
	fmt.Println()
	// 大写，小写，标题 | 当前字符 -> 下一个匹配字符
	for _, v := range s {
		fmt.Printf("%c, %c, %c | %c -> %c\n",
			unicode.ToUpper(v),
			unicode.ToLower(v),
			unicode.ToTitle(v),
			v,
			unicode.SimpleFold(v),
		)
	}
}

// 输出结果：
// 3a6  3c6  3d5  6b  4b  212a
// Φ, φ, Φ | Φ -> φ
// Φ, φ, Φ | φ -> ϕ
// Φ, ϕ, Φ | ϕ -> Φ
// K, k, K | k -> K
// K, k, K | K -> k
// K, k, K | K -> K

------------------------------------------------------------

// IsDigit 判断 r 是否为一个十进制的数字字符
func IsDigit(r rune) bool

// IsNumber 判断 r 是否为一个数字字符 (类别 N)
func IsNumber(r rune) bool

// IsLetter 判断 r 是否为一个字母字符 (类别 L)
// 汉字也是一个字母字符
func IsLetter(r rune) bool

// IsSpace 判断 r 是否为一个空白字符
// 在 Latin-1 字符集中，空白字符为：\t, \n, \v, \f, \r,
// 空格, U+0085 (NEL), U+00A0 (NBSP)
// 其它空白字符的定义有“类别 Z”和“Pattern_White_Space 属性”
func IsSpace(r rune) bool

// IsControl 判断 r 是否为一个控制字符
// Unicode 类别 C 包含更多字符，比如代理字符
// 使用 Is(C, r) 来测试它们
func IsControl(r rune) bool

// IsGraphic 判断字符 r 是否为一个“图形字符”
// “图形字符”包括字母、标记、数字、标点、符号、空格
// 他们分别对应于 L、M、N、P、S、Zs 类别
// 这些类别是 RangeTable 类型，存储了相应类别的字符范围
func IsGraphic(r rune) bool

// IsPrint 判断字符 r 是否为 Go 所定义的“可打印字符”
// “可打印字符”包括字母、标记、数字、标点、符号和 ASCII 空格
// 他们分别对应于 L, M, N, P, S 类别和 ASCII 空格
// “可打印字符”和“图形字符”基本是相同的，不同之处在于
// “可打印字符”只包含 Zs 类别中的 ASCII 空格（U+0020）
func IsPrint(r rune) bool

// IsPunct 判断 r 是否为一个标点字符 (类别 P)
func IsPunct(r rune) bool

// IsSymbol 判断 r 是否为一个符号字符
func IsSymbol(r rune) bool

// IsMark 判断 r 是否为一个 mark 字符 (类别 M)
func IsMark(r rune) bool

// IsOneOf 判断 r 是否在 set 范围内
func IsOneOf(set []*RangeTable, r rune) bool

------------------------------

// 示例
func main() {
	fmt.Println() // 数字
	for _, r := range "Hello 123１２３一二三！" {
		if unicode.IsDigit(r) {
			fmt.Printf("%c", r)
		}
	} // 123１２３

	fmt.Println() // 数字
	for _, r := range "Hello 123１２３一二三！" {
		if unicode.IsNumber(r) {
			fmt.Printf("%c", r)
		}
	} // 123１２３

	fmt.Println() // 字母
	for _, r := range "Hello\n\t世界！" {
		if unicode.IsLetter(r) {
			fmt.Printf("%c", r)
		}
	} // Hello世界

	fmt.Println() // 空白
	for _, r := range "Hello \t世　界！\n" {
		if unicode.IsSpace(r) {
			fmt.Printf("%q", r)
		}
	} // ' ''\t''\u3000''\n'

	fmt.Println() // 控制字符
	for _, r := range "Hello\n\t世界！" {
		if unicode.IsControl(r) {
			fmt.Printf("%#q", r)
		}
	} // '\n''\t'

	fmt.Println() // 可打印
	for _, r := range "Hello　世界！\t" {
		if unicode.IsPrint(r) {
			fmt.Printf("%c", r)
		}
	} // Hello世界！

	fmt.Println() // 图形
	for _, r := range "Hello　世界！\t" {
		if unicode.IsGraphic(r) {
			fmt.Printf("%c", r)
		}
	} // Hello　世界！

	fmt.Println() // 掩码
	for _, r := range "Hello ៉៊់៌៍！" {
		if unicode.IsMark(r) {
			fmt.Printf("%c", r)
		}
	} // ៉៊់៌៍

	fmt.Println() // 标点
	for _, r := range "Hello 世界！" {
		if unicode.IsPunct(r) {
			fmt.Printf("%c", r)
		}
	} // ！

	fmt.Println() // 符号
	for _, r := range "Hello (<世=界>)" {
		if unicode.IsSymbol(r) {
			fmt.Printf("%c", r)
		}
	} // <=>
}

------------------------------

// 示例：判断汉字和标点
func main() {
	// 将 set 设置为“汉字、标点符号”
	set := []*unicode.RangeTable{unicode.Han, unicode.P}
	for _, r := range "Hello 世界！" {
		if unicode.IsOneOf(set, r) {
			fmt.Printf("%c", r)
		}
	} // 世界！
}

------------------------------

// 示例：输出所有 mark 字符
func main() {
	for _, cr := range unicode.M.R16 {
		Lo, Hi, Stride := rune(cr.Lo), rune(cr.Hi), rune(cr.Stride)
		for i := Lo; i >= Lo && i <= Hi; i += Stride {
			if unicode.IsMark(i) {
				fmt.Printf("%c", i)
			}
		}
	}
}

------------------------------------------------------------
```