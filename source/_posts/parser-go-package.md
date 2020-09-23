---
title: Go parser包的介绍
date: 2015-07-21 12:39:00
updated: 2015-08-12 17:12:41
tags: 
- linux
- tesseract
- ocr
categories: 
- linux

---
原文：https://golang.org/pkg/go/parser/

包 parser

`import "go/parser"`

# Overview
包分析器实现了一个源文件的解析器。输入可以以多种形式提供（见Parse* functions）；输出的是一个抽象语法树（AST）代表去源。通过一个解析函数来调用解析器。


<!--more-->


抽象语法树（AST）表示组成程序的结构，可以让程序员更容易使用，F# 适宜这种开发的一个原因就是它的联合类型。这种类型非常适合表示语言，因为它可以用来表示相关而结构不相同的项目。下面就是抽象语法树的例子： 

    type Ast =
        | Ident of string
        | Val of System.Double
        | Multi of Ast * Ast
        | Div of Ast * Ast
        | Plus of Ast * Ast
        | Minus of Ast * Ast

 
树非常简单，只包含一种类型；复杂的树可能包含很多类型，但都遵循这一基本模式。对于这个树，它是Ast [ 原文有误，忘记修改了] 类型，包含了标识符（Ident 类型），标识符的名字由字符串表示，还包含了值（Val 类型），值由System.Double 表示。Ast 类型还包含了另外四个类型（Multi、Div、Plus、Minus），表示算术运算，并使用递归，由此组成其他表达式。

