---
title: Go语言中io.Reader和io.Writer的详解与实现
date: 2017-09-26 13:04:00
updated: 2017-09-27 13:05:12
tags: 
- golang
- panic
categories: 
- go

---
## 一、前言

也许对这两个接口和相关的一些接口很熟悉了，但是你脑海里确很难形成一个对io接口的继承关系整天的概貌，原因在于godoc缺省并没有像javadoc一样显示官方库继承关系，这导致了我们对io接口的继承关系记忆不深，在使用的时候还经常需要翻文档加深记忆。

本文试图梳理清楚Go io接口的继承关系，提供一个io接口的全貌。

## 二、io接口回顾

首先我们回顾一下几个常用的io接口。标准库的实现是将功能细分，每个最小粒度的功能定义成一个接口，然后接口可以组成成更多功能的接口。


<!--more-->


### 最小粒度的接口
```go
type Reader interface {
  Read(p []byte) (n int, err error)
}
type Writer interface {
  Write(p []byte) (n int, err error)
}
type Closer interface {
  Close() error
}
type Seeker interface {
  Seek(offset int64, whence int) (int64, error)
}
type ReaderFrom interface {
  ReadFrom(r Reader) (n int64, err error)
}
type WriterTo interface {
  WriteTo(w Writer) (n int64, err error)
}
type ReaderAt interface {
  ReadAt(p []byte, off int64) (n int, err error)
}
type WriterAt interface {
  WriteAt(p []byte, off int64) (n int, err error)
}
以及

type ByteReader interface {
  ReadByte() (byte, error)
}
type ByteWriter interface {
  WriteByte(c byte) error
}
```
ByteScanner比ByteReader多了一个UnreadByte方法。
```go
type ByteScanner interface {
  ByteReader
  UnreadByte() error
}
type RuneReader interface {
  ReadRune() (r rune, size int, err error)
}
type RuneScanner interface {
  RuneReader
  UnreadRune() error
}
```
### 组合接口

Go标准库还定义了一些由上面的单一职能的接口组合而成的接口。
```
type ReadCloser interface {
  Reader
  Closer
}
type ReadSeeker interface {
  Reader
  Seeker
}
type ReadWriter interface {
  Reader
  Writer
}
type ReadWriteCloser interface {
  Reader
  Writer
  Closer
}
type ReadWriteSeeker interface {
  Reader
  Writer
  Seeker
}
type WriteCloser interface {
  Writer
  Closer
}
type WriteSeeker interface {
  Writer
  Seeker
}
```
从它们的定义上可以看出，它们是最小粒度的组合。

### 最小接口的扩展

有些结构体struct实现并且扩展了接口，这些结构体是。
```go
type LimitedReader struct {
  R Reader // underlying reader
  N int64 // max bytes remaining
}
type PipeReader struct {
  // contains filtered or unexported fields
}
type PipeWriter struct {
  // contains filtered or unexported fields
}
type SectionReader struct {
  // contains filtered or unexported fields
}
```
下面我会将它们的继承关系画出来。

## 一些辅助方法

一些辅助方法可以生成特殊类型的Reader或者Writer:

func LimitReader(r Reader, n int64) Reader
func MultiReader(readers ...Reader) Reader
func TeeReader(r Reader, w Writer) Reader
func MultiWriter(writers ...Writer) Writer
## 三、继承关系

当然，Go语言中并没有Java中那样的继承关系，而是基于duck type形式实现，我用下图尝试展示Go io接口的继承关系。
![golang_impl.png][1]

其中黄色是 bufio 包下的类型,

绿色是 archive.tar 包下的类型，

蓝色是 bytes 包下的类型，

粉红色是 strings包下的类型，

紫色是 crypto.tls 包下的类型。

Rand是math.rand包下的类型。

File是os包下的内容。

｀Rand｀左边的那个Reader是image.jpeg下的内容。

我们最常用的是包io、bytes、bufio下的类型，所以这几个包下的类型要记牢，在第三库中经常会出现它们的身影。

上图中并没有把mime/multipart.File和 net/http.File列出来，主要是图太复杂了,它们实现的接口和os.File类似。

当然你可能会问，你怎么整理的它们的继承关系？事实上，你可以通过godoc -analysis=type -http=:6060生成带继承关系的Go doc，并且它还可以将你本地下载的库中的继承关系也显示出来。
![golang_impls_doc.png][2]


  [1]: https://imgs.gnux.cn/usr/uploads/2017/09/3343972621.png
  [2]: https://imgs.gnux.cn/usr/uploads/2017/09/1538896414.png