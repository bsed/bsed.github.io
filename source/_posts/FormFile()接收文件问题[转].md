---
title: "FormFile()接收文件问题[转]"
categories: [ "Golang" ]
tags: [ "golang","formfile" ]
draft: false
slug: "formfile-to-receive-the-file-issue"
date: "2014-08-28 11:02:00"
---

    type Sizer interface {
        Size() int64
    }
    //上传处理函数
    func GetFile(w http.ResponseWriter, r *http.Request){
        file, handler, err := r.FormFile("file")
        if err != nil {
            msg := "获取上传文件错误:" + err.Error()
            fmt.Fprintf(w, msg)
            return
        }
        defer file.Close()
        if fileSizer, ok := file.(Sizer); ok {
            fileSize := fileSizer.Size() / (1024*1024)
            fmt.Println("上传文件的大小为: %d",fileSize, handler.Filename)
            if fileSize > 100 {
                msg := "获取上传文件错误:文件大小超出100M"
                fmt.Fprintf(w, msg)
                return
            }


<!--more-->


        } else {
            msg := "获取上传文件错误:无法读取文件大小"
            fmt.Fprintf(w, msg)
            return
        }
        fmt.Fprintf(w, "ok")
    }

*问题:*

    上传大文件比如115M时,file.(Sizer)失败,无法获取上传的文件大小

    内存随上传文件的大小而增加,即执行完GetFile()后没有释放内存

请问要如何解决这两个问题?

下面是一些博友的回答

ggaaooppeenngg  2014-07-23 13:44

[http://golang.org/src/pkg/net/http/request.go?s=23428:23487#L767](
http://golang.org/src/pkg/net/http/request.go?s=23428:23487#L767) 1. 有默认大小 [http://golang.org/pkg/runtime/#SetFinalizer](http://golang.org/pkg/runtime/#SetFinalizer) 2 文件要GC来free

原文:[http://golangtc.com/t/53cf4890320b522d86000013](http://golangtc.com/t/53cf4890320b522d86000013)
