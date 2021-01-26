---
title: "golang json解析Marshal和Unmarshal"
categories: [ "Golang" ]
tags: [ "json","golang","marshal","unmarshal" ]
draft: false
slug: "json-golang-marshal-and-unmarshal"
date: "2014-04-23 19:00:00"
---

go语言提供一个json解析的包。见[http://golang.org/pkg/encoding/json/](http://golang.org/pkg/encoding/json/)

官方同时提供了一篇文章[JSON and Go](http://blog.golang.org/json-and-go)讲述json包的用法， 该文章同时存在中文翻译：[JSON与Go](http://rgyq.blog.163.com/blog/static/316125382013934153244/)。

看过上述两篇文章后，基本使用应该就没问题了。


<!--more-->


同时，贴几个官方的例子，方便理解。
Decoder:

    package main
    
    import (
        "encoding/json"
        "fmt"
        "io"
        "log"
        "strings"
    )
    
    func main() {
        const jsonStream = `
            {"Name": "Ed", "Text": "Knock knock."}
            {"Name": "Sam", "Text": "Who's there?"}
            {"Name": "Ed", "Text": "Go fmt."}
            {"Name": "Sam", "Text": "Go fmt who?"}
            {"Name": "Ed", "Text": "Go fmt yourself!"}
        `
        type Message struct {
            Name, Text string
        }
        dec := json.NewDecoder(strings.NewReader(jsonStream))
        for {
            var m Message
            if err := dec.Decode(&m); err == io.EOF {
                break
            } else if err != nil {
                log.Fatal(err)
            }
            fmt.Printf("%s: %s\n", m.Name, m.Text)
        }
    }

Marshal:

    package main
    
    import (
        "encoding/json"
        "fmt"
        "os"
    )
    
    func main() {
        type ColorGroup struct {
            ID     int
            Name   string
            Colors []string
        }
        group := ColorGroup{
            ID:     1,
            Name:   "Reds",
            Colors: []string{"Crimson", "Red", "Ruby", "Maroon"},
        }
        b, err := json.Marshal(group)
        if err != nil {
            fmt.Println("error:", err)
        }
        os.Stdout.Write(b)
    }

RawMessage :

    package main
    
    import (
        "encoding/json"
        "fmt"
        "log"
    )
    
    func main() {
        type Color struct {
            Space string
            Point json.RawMessage // delay parsing until we know the color space
        }
        type RGB struct {
            R uint8
            G uint8
            B uint8
        }
        type YCbCr struct {
            Y  uint8
            Cb int8
            Cr int8
        }
    
        var j = []byte(`[
            {"Space": "YCbCr", "Point": {"Y": 255, "Cb": 0, "Cr": -10}},
            {"Space": "RGB",   "Point": {"R": 98, "G": 218, "B": 255}}
        ]`)
        var colors []Color
        err := json.Unmarshal(j, &colors)
        if err != nil {
            log.Fatalln("error:", err)
        }
    
        for _, c := range colors {
            var dst interface{}
            switch c.Space {
            case "RGB":
                dst = new(RGB)
            case "YCbCr":
                dst = new(YCbCr)
            }
            err := json.Unmarshal(c.Point, dst)
            if err != nil {
                log.Fatalln("error:", err)
            }
            fmt.Println(c.Space, dst)
        }
    }

Unmarshal:

    package main
    
    import (
        "encoding/json"
        "fmt"
    )
    
    func main() {
        var jsonBlob = []byte(`[
            {"Name": "Platypus", "Order": "Monotremata"},
            {"Name": "Quoll",    "Order": "Dasyuromorphia"}
        ]`)
        type Animal struct {
            Name  string
            Order string
        }
        var animals []Animal
        err := json.Unmarshal(jsonBlob, &animals)
        if err != nil {
            fmt.Println("error:", err)
        }
        fmt.Printf("%+v", animals)
    }

原文： [http://www.baiyuxiong.com/?p=923&utm_source=tuicool](http://www.baiyuxiong.com/?p=923&utm_source=tuicool)