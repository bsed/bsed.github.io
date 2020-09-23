---
title: Golang学习 - unicode/utf16 包
date: 2016-04-26 17:36:00
updated: 2017-02-20 17:39:12
tags: 
- golang
- sort
categories: 
- go

---
```golang
// IsSurrogate 判断 r 是否为代理区字符
// 两个代理区字符可以用来组合成一个 utf16 编码
func IsSurrogate(r rune) bool

// EncodeRune 将字符 r 编码成 UTF-16 代理对
// r：要编码的字符
// 如果 r < 0x10000 ，则无需编码，其 UTF-16 序列就是其自身
// r1：编码后的 UTF-16 代理对的高位码元
// r2：编码后的 UTF-16 代理对的低位码元
// 如果 r 不是有效的 Unicode 字符，或者是代理区字符，或者无需编码
// 则返回 U+FFFD, U+FFFD
func EncodeRune(r rune) (r1, r2 rune)


<!--more-->


// DecodeRune 将 UTF-16 代理对解码成一个 Unicode 字符
// r1：是 UTF-16 代理对的高位码元
// r2：是 UTF-16 代理对的低位码元
// 返回值为解码后的 Unicode 字符
// 如果 r1 或 r2 不是有效的 UTF-16 代理区字符，则返回 U+FFFD
func DecodeRune(r1, r2 rune) rune

// Decode 将 UTF-16 序列 s 解码成 Unicode 字符序列并返回
func Decode(s []uint16) []rune

// Encode 将 s 编码成 UTF-16 序列并返回
func Encode(s []rune) []uint16

------------------------------

// 示例
func main() {
	fmt.Printf("%t, ", utf16.IsSurrogate(0xD400)) // false
	fmt.Printf("%t, ", utf16.IsSurrogate(0xDC00)) // true
	fmt.Printf("%t\n", utf16.IsSurrogate(0xDFFF)) // true

	r1, r2 := utf16.EncodeRune('