---
title: "golang 文件操作"
categories: [ "Golang" ]
tags: [ "golang" ]
draft: false
slug: "golang-file-operation"
date: "2012-08-04 19:57:00"
---

文件操作

golang的文件操作时在os包中的。

所以用的时候要import os包
包含以下的这些函数

    func Create(name string) (file *File, err error)  直接通过纹面创建文件
    func NewFile(fd uintptr, name string) *File
    func Open(name string) (file *File, err error) 以只读方式打开一个存在的文件，打开就可以读取了。
    func OpenFile(name string, flag int, perm FileMode) (file *File, err error)

以各种方式打开各种存在不存在的文件，具体怎么样看flag和perm。
flag可选值（掩码）：

    O_RDONLY int = syscall.O_RDONLY // 只读
    O_WRONLY int = syscall.O_WRONLY // 只写
    O_RDWR   int = syscall.O_RDWR   // 读写
    O_APPEND int = syscall.O_APPEND // 在文件末尾追加，打开后cursor在文件结尾位置
    O_CREATE int = syscall.O_CREAT  // 如果不存在则创建
    O_EXCL   int = syscall.O_EXCL   //与O_CREATE一起用，构成一个新建文件的功能，它要求文件必须不存在
    O_SYNC   int = syscall.O_SYNC   // 同步方式打开，没有缓存，这样写入内容直接写入硬盘，系统掉电文件内容有一定保证
    O_TRUNC  int = syscall.O_TRUNC  // 打开并清空文件

FileMode 就是unix权限位。[http://www.ibm.com/developerworks/cn/aix/library/au-speakingunix4/permissions.gif]()

由于是文件第一位是0 后面可以是666 755 熟悉linux的都懂得

    func Pipe() (r *File, w *File, err error) 管道
    func (f *File) Chdir() error              改变当前的工作目录
    func (f *File) Chmod(mode FileMode) error  改变权限
    func (f *File) Chown(uid, gid int) error    改变所有者
    func (f *File) Close() error                 关闭文件
    func (f *File) Fd() uintptr            返回文件句柄
    func (f *File) Name() string           返回文件名
    func (f *File) Read(b []byte) (n int, err error) 读取文件
    func (f *File) ReadAt(b []byte, off int64) (n int, err error) 从off开始读取文件
    func (f *File) Readdir(n int) (fi []FileInfo, err error)   读取文件目录返回n个fileinfo
    func (f *File) Readdirnames(n int) (names []string, err error) 读取文件目录返回n个文件名
    func (f *File) Seek(offset int64, whence int) (ret int64, err error) 设置读写文件的偏移量，whence为0表示相对于文件的开始处，1表示相对于当前的位置，2表示相对于文件结尾。他返回偏移量。如果有错误返回错误
    func (f *File) Stat() (fi FileInfo, err error) 返回当前文件fileinfo结构体
    func (f *File) Sync() (err error)  把当前内容持久化，一般就是马上写入到磁盘
    func (f *File) Truncate(size int64) error   改变当前文件的大小，他不改变当前文件读写的偏移量
    func (f *File) Write(b []byte) (n int, err error) 写入内容
    func (f *File) WriteAt(b []byte, off int64) (n int, err error) 在offset位置写入内容
    func (f *File) WriteString(s string) (ret int, err error) 写入字符

以上参考 http://bbs.csdn.net/topics/390548796

以下为补充实例

    package main
     
    import (
        "fmt"
        "io/ioutil"
        "os"
    )
     
    func create() {
        finame := "file_test.txt"
        fi, err := os.Create(finame)
        if err != nil {
            panic(err)
        }
        defer fi.Close()
    }
     
    func write() {
        finame := "file_test.txt"
        fi, err := os.OpenFile(finame, os.O_CREATE|os.O_WRONLY, 644)
        if err != nil {
            panic(err)
        }
        defer fi.Close()
        fi.WriteString("file test!\n")
        fi.Write([]byte("file test byte!\n"))
    }
     
    func add() {
        finame := "file_test.txt"
        fi, err := os.OpenFile(finame, os.O_RDWR|os.O_APPEND, 644)
        if err != nil {
            panic(err)
        }
        defer fi.Close()
        fi.WriteString("add string test!\n")
    }
     
    func read() {
        finame := "file_test.txt"
        fi, err := os.Open(finame)
        if err != nil {
            panic(err)
        }
        defer fi.Close()
        fd, err := ioutil.ReadAll(fi)
        fmt.Print(string(fd))
    }
     
    func del() {
        finame := "file_test.txt"
        err := os.Remove(finame)
        if err != nil {
            fmt.Println("finame", err)
        }
     
    }
     
    func main() {
        create()
        write()
        add()
        read()
        del()
    }
