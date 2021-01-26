---
title: "Golang 为类型 定义方法 [Go学习笔记]"
categories: [ "Golang" ]
tags: [ "golang","func","方法" ]
draft: false
slug: "go-for-the-type-definition-method-golang-learning-notes"
date: "2015-08-21 17:07:40"
---

![golang-1-638.jpg][1]
### 给类型定义方法

在Go语言中，我们可以给任何类型（包括内置类型，但不包括指针和接口）定义方法。例如，在实际编程中，我们经常使用`[ ]byte`的切片，我们可以定义一个新的类型：

```go
type ByteSlice []byte
```
然后我们就可以定义方法了。例如，假如我们不想使用内建的append函数，我们可以实现一个自己的append方法：

```go
func Append(slice, data[]byte) []byte {
    l := len(slice)
    if l + len(data) > cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))*2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    return slice
}
```


<!--more-->


我们可以在Append实现自己的内存扩展策略。这个新的类型与[ ]byte没有其它的区别，只是它多了一个Append方法：

```go
        var a ByteSlice = []byte{1,2,3}
        b := []byte{4}
        a.Append(b) //won't change a
        fmt.Println(a)
        a = a.Append(b)
        fmt.Println(a);
```
输出：

```go
[1 2 3]
[1 2 3 4]
```
注意，上面的Append方法只能通过ByteSlice调用，而不能通过[ ]byte的方式调用。另外，为了得到更新后的值，必须将更新后的值做为返回值返回，这种做法比较笨拙，我们可以换一种更优美的方式实现Append方法：

```go
func (p *ByteSlice) Append(data[]byte) {
    slice := *p
    l := len(slice)
    if l + len(data) > cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))*2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    *p = slice
}
```
通过使用指针的方式，可以达到修改对象本身的目的：

```go
        var a ByteSlice = []byte{1,2,3}
        var c ByteSlice = []byte{1,2,3}
        b := []byte{4}
        (&a).Append(b)
        c.Append(b)
        fmt.Println(a)
        fmt.Println(c)
```
输出：

```go
[1 2 3 4]
[1 2 3 4]
```
实际上，我们可以更进一步，我们可以将函数修改成标准Write方法的样子：

```go
func (p *ByteSlice) Write(data []byte) (n int, err error) {
    slice := *p
    l := len(slice)
    if l + len(data) > cap(slice) {  // reallocate
        // Allocate double what's needed, for future growth.
        newSlice := make([]byte, (l+len(data))*2)
        // The copy function is predeclared and works for any slice type.
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    *p = slice
    return len(data), nil
}
```
这样类型*ByteSlice就会满足标准接口io.Writer：

```go
package io
type Writer interface {
	Write(p []byte) (n int, err error)
}
```
这样我们就可以打印到该类型的变量中：

```go
        var b ByteSlice
        fmt.Fprintf(&b, "aa%dbb", 7)
        fmt.Println(b)
```
输出：

```go
[97 97 55 98 98]
```
注意，这里必须传递&b给fmt.Fprintf，如果传递b，则编译时会报下面的错误：

> cannot use b (type ByteSlice) as type io.Writer in argument to fmt.Fprintf:
>	ByteSlice does not implement io.Writer (Write method has pointer receiver)

Go语言规范有这样的规定：

> The method set of any other named type T consists of all methods with receiver type T. The method set of the corresponding pointer type *T is the set of all methods with receiver *T or T (that is, it also contains the method set of T).

参见这里。通俗点来说，就是指针类型（*T）的对象包含的接收者为T的方法，反之，则不包含。<effective go>中有这样的描述：

> We pass the address of a ByteSlice because only *ByteSlice satisfies io.Writer. The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers.

我们这里只定义了(p *ByteSlice) Write方法，而ByteSlice并没有实现接口io.Write，所以就会报上面的错误。注意，这里的描述有一个上下文，就是给接口赋值。

那为什么在Append的示例中，(&a).Append(b)和c.Append(b)都是OK的呢？因为这里与接口无关。我们不能再以C++的思维来理解Go，因为Go中的对象没有this指针。更直白的说，对象本身是作为参数显式传递的。所以，即使c.Append(b)，Go也会传递&c给Append方法。

不管怎么样，我觉得这里还是很让人迷糊的。

值方法与指针方法
------
上一节中，我们看到了值方法（value method，receiver为value）与指针方法（pointer method，receiver与pointer）的区别，

```go
func (s *MyStruct) pointerMethod() { } // method on pointer
func (s MyStruct)  valueMethod()   { } // method on value
```
那么什么时候用值方法，什么时候用指针方法呢？主要考虑以下一些因素：

  - 1.如果方法需要修改receiver，那么必须使用指针方法；
  - 2.如果receiver是一个很大的结构体，考虑到效率，应该使用指针方法；
  - 3.一致性。如果一些方法必须是指针receiver，那么其它方法也应该使用指针receiver；
  - 4.对于一些基本类型、切片、或者小的结构体，使用value receiver效率会更高一些。

详细参考这里。

示例
------
这种给原生数据类型增加方法的做法，在Go语言编程中很常见，来看一下http.Header：

```go
// A Header represents the key-value pairs in an HTTP header.
type Header map[string][]string

// Add adds the key, value pair to the header.
// It appends to any existing values associated with key.
func (h Header) Add(key, value string) {
    textproto.MIMEHeader(h).Add(key, value)
}
…
```


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2704317730.jpg