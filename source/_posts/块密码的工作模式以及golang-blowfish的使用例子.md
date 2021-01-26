---
title: "块密码的工作模式以及golang blowfish的使用例子"
categories: [ "Golang" ]
tags: [ "golang","cbc","blowfish" ]
draft: false
slug: "block-cipher-mode-of-operation"
date: "2017-07-09 13:21:00"
---

### 简介

密码学中，块密码的工作模式（英语：mode of operation）允许使用同一个块密码密钥对多于一块的数据进行加密，并保证其安全性。 块密码自身只能加密长度等于密码块长度的单块数据，若要加密变长数据，则数据必须先被划分为一些单独的密码块。通常而言，最后一块数据也需要使用合适填充方式将数据扩展到符合密码块大小的长度。一种工作模式描述了加密每一数据块的过程，并常常使用基于一个通常称为初始化向量的附加输入值以进行随机化，以保证安全。

工作模式主要用来进行加密和认证。 对加密模式的研究曾经包含数据的完整性保护，即在某些数据被修改后的情况下密码的误差传播特性。后来的研究则将完整性保护作为另一个完全不同的，与加密无关的密码学目标。部分现代的工作模式用有效的方法将加密和认证结合起来，称为认证加密模式。

虽然工作模式通常应用于对称加密，它亦可以应用于公钥加密，例如在原理上对RSA进行处理，但在实用中，公钥密码学通常不用于加密较长的信息，而是使用混合加密方案。

### 主要的模式有：

1 电子密码本（ECB） 
2 密码块链接（CBC） 
3 填充密码块链接（PCBC） 
4 密文反馈（CFB） 
5 输出反馈（OFB） 
6 计数器模式（CTR）


<!--more-->


### wiki

[块密码的工作模式 WIKI](https://zh.wikipedia.org/wiki/%E5%9D%97%E5%AF%86%E7%A0%81%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F#.E5.AF.86.E6.96.87.E5.8F.8D.E9.A6.88.EF.BC.88CFB.EF.BC.89)

### 简单实现

golang 实现的一个blowfish CFB解密

```
package main
import (
    "crypto/cipher"
    "encoding/base64"
    "fmt"
    "golang.org/x/crypto/blowfish"
)
var key string = "dN1c6hp41sd03VzqGnjWx3TdXOoaeNbH"
var IV = []byte{0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00}
func main() {
    ci, err := blowfish.NewCipher([]byte(key))
    if err != nil {
        panic(err)
    }
    s := cipher.NewCFBEncrypter(ci, IV)
    data, err := base64.URLEncoding.DecodeString("puMPEEemzijO")
    if err != nil {
        panic(err)
    }
    dst := make([]byte, len(data))
    s.XORKeyStream(dst, data)
    fmt.Println(string(dst))
}
```

另外因为go官方认为ECB是不安全的。所以官方库没有收录。下面是EBC模式的实现:

```
type ECB struct {
    b         cipher.Block
    blockSize int
}
func NewECB(b cipher.Block) *ECB {
    return &ECB{
        b:         b,
        blockSize: b.BlockSize(),
    }
}
type ECBEncrypter ECB
// NewECBEncrypter returns a BlockMode which encrypts in electronic code book
// mode, using the given Block.
func NewECBEncrypter(b cipher.Block) cipher.BlockMode {
    return (*ECBEncrypter)(NewECB(b))
}
func (x *ECBEncrypter) BlockSize() int { return x.blockSize }
func (x *ECBEncrypter) CryptBlocks(dst, src []byte) {
    if len(src)%x.blockSize != 0 {
        panic("crypto/cipher: input not full blocks")
    }
    if len(dst) < len(src) {
        panic("crypto/cipher: output smaller than input")
    }
    for len(src) > 0 {
        x.b.Encrypt(dst, src[:x.blockSize])
        src = src[x.blockSize:]
        dst = dst[x.blockSize:]
    }
}
type ECBDecrypter ECB
// NewECBDecrypter returns a BlockMode which decrypts in electronic code book
// mode, using the given Block.
func NewECBDecrypter(b cipher.Block) cipher.BlockMode {
    return (*ECBDecrypter)(NewECB(b))
}
func (x *ECBDecrypter) BlockSize() int {
    return x.blockSize
}
func (x *ECBDecrypter) CryptBlocks(dst, src []byte) {
    if len(src)%x.blockSize != 0 {
        panic("crypto/cipher: input not full blocks")
    }
    if len(dst) < len(src) {
        panic("crypto/cipher: output smaller than input")
    }
    for len(src) > 0 {
        x.b.Decrypt(dst, src[:x.blockSize])
        src = src[x.blockSize:]
        dst = dst[x.blockSize:]
    }
}
```
下面我的blowfish算法的使用：
```golang
package bwf

import (
	"bytes"
	"crypto/cipher"
	"encoding/base64"
	"errors"
	"golang.org/x/crypto/blowfish"
	"log"
	"strings"
)

type B64 struct {
	Key []byte
	IV  []byte
}

// Encrypt 对字符串进行加密
func (b *B64) Encrypt(pt []byte) ([]byte, error) {
	key := b.Key
	block, err := blowfish.NewCipher(key)
	if err != nil {
		//panic(err.Error())
		return nil, errors.New("Couldn't initialize the blowfish cipher")
	}

	// 密码块链接（CBC）
	cbcEnc := cipher.NewCBCEncrypter(block, b.IV)

	// 密码学中，块密码的工作模式（英语：mode of operation）允许使用同一个块密码密钥对多于一块的数据进行加密，
	// 并保证其安全性。 块密码自身只能加密长度等于密码块长度的单块数据，若要加密变长数据，
	// 则数据必须先被划分为一些单独的密码块。通常而言，最后一块数据也需要使用合适填充方式将数据扩展到符合密码块大小的长度。
	// 检查解密的数据是否是块大小的倍数。
	// 例如 要加密的字符串长度为10字节，秘钥块为8字节，10除以8， 余数为2，那就需要在最后一个密码块中添加6个字节以便于凑齐完整的密码块，
	r := 8 - (len(pt) % 8)
	// pt为密码块长度倍数的单块数据
	pt = append(pt, make([]byte, r)...)

	// 创建加密块
	ct := make([]byte, len(pt))

	// 加密块
	cbcEnc.CryptBlocks(ct, pt) // 两者的字节数长度一致
	sEnc := base64.StdEncoding.EncodeToString([]byte(ct))
	log.Println("pre replace", sEnc)
	sEnc = strings.Replace(sEnc, "+", "-", -1)
	sEnc = strings.Replace(sEnc, "/", "_", -1)
	sEnc = strings.Replace(sEnc, "/", ".", -1)
	return []byte(sEnc), nil
}

// Decrypt 对字符串进行解密
func (b *B64) Decrypt(ct string) ([]byte, error) {
	key := b.Key
	ct = strings.Replace(ct, "-", "+", -1)
	ct = strings.Replace(ct, "_", "/", -1)
	ct = strings.Replace(ct, ".", "=", -1)

	//log.Println("decrypt replace", ct)

	// 字符串转为字节
	sDec, _ := base64.StdEncoding.DecodeString(ct)

	block, err := blowfish.NewCipher(key)
	if err != nil {
		//panic(err.Error())
		return nil, errors.New("Couldn't initialize the blowfish cipher")
	}

	cbcDec := cipher.NewCBCDecrypter(block, b.IV)

	pt := make([]byte, len(sDec))
	cbcDec.CryptBlocks(pt, sDec)
	//log.Println("dump", pt)
	r := bytes.LastIndex(pt, []byte{0})
	log.Println("width ", r)
	pt = pt[0:r]
	return pt, nil
}

// GetCT 获取加密后的字符串
func (b *B64) GetCT(ct string) string {
	encCt, _ := b.Encrypt([]byte(ct))
	return string(encCt)
}

```
测试用例：
```golang
package bwf

import (
	"testing"
)

func TestB64_Encrypt(t *testing.T) {
	pt := []byte("this is test")
	b64 := &B64{
		Key: []byte("hello"),
		IV:  []byte("worldwww"),
	}
	ct, _ := b64.Encrypt(pt)
	t.Logf("Cipher Text: %s\n", ct)

	// 运行结果
	/**
	$ go test -v -test.run TestB64_Encrypt
	=== RUN   TestB64_Encrypt
	2017/07/10 10:26:33 pre replace KQZrk1XhEUYJ62zldGL+ww==
	--- PASS: TestB64_Encrypt (0.01s)
	        blowfish_test.go:14: Cipher Text: KQZrk1XhEUYJ62zldGL-ww==
	PASS
	ok      zhtj/utils/bwf  0.193s
	*/

}
func TestB64_Decrypt(t *testing.T) {
	sDec := "KQZrk1XhEUYJ62zldGL-ww=="
	b64 := &B64{
		Key: []byte("hello"),
		IV:  []byte("worldwww"),
	}
	dstDec, _ := b64.Decrypt(sDec)
	t.Logf("Decrypt plaintext: %s\n", dstDec)

	// 运行结果
	/**
	$ go test -v -test.run TestB64_Decrypt
	=== RUN   TestB64_Decrypt
	2017/07/10 10:55:27 post replace KQZrk1XhEUYJ62zldGL+ww==
	2017/07/10 10:55:27 dump [116 104 105 115 32 105 115 32 116 101 115 116 0 0 0 0]
	2017/07/10 10:55:27 width  15
	--- PASS: TestB64_Decrypt (0.01s)
	        blowfish_test.go:34: Decrypt plaintext: this is test
	PASS
	ok      zhtj/utils/bwf  0.191s
	*/
}

func TestB64_GetCT(t *testing.T) {
	t.Log((&B64{Key: []byte("hello"), IV: []byte("worldwww")}).GetCT("hello world"))
}

```