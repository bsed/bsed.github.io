---
title: "利用Go语言追加内容到文件末尾"
categories: [ "Golang" ]
tags: [ "golang","file" ]
draft: false
slug: "append-the-content-to-the-end-of-the-file-using-the-go-language"
date: "2017-09-24 12:17:00"
---

## 前言

我研究了file库，终于让我找到了利用Go语言追加内容到文件末尾的办法

主要的2个函数：


<!--more-->


```golang
func (f *File) Seek(offset int64, whence int) (ret int64, err error)
func (f *File) WriteAt(b []byte, off int64) (n int, err error)
```
Seek()查到文件末尾的偏移量
WriteAt()则从偏移量开始写入
以下是例子：
```golang
// fileName:文件名字(带全路径)
// content: 写入的内容
func appendToFile(fileName string, content string) error {
  // 以只写的模式，打开文件
  f, err := os.OpenFile(fileName, os.O_WRONLY, 0644)
  if err != nil {
   fmt.Println("config.yml file create failed. err: " + err.Error())
  } else {
   // 查找文件末尾的偏移量
   n, _ := f.Seek(0, os.SEEK_END)
   // 从末尾的偏移量开始写入内容
   _, err = f.WriteAt([]byte(content), n)
  }  
defer f.Close()  
return err}
```