---
title: "golang的内置模板语法[Go学习笔记]"
categories: [ "Golang" ]
tags: [ "template","golang" ]
draft: false
slug: "golangs-builtin-template-syntax-for-go-learning-notes"
date: "2015-09-21 11:52:00"
---

## 【模板标签】
模板标签用"{{"和"}}"括起来
##【注释】
```golang
{{/* a comment */}}
```
使用`{{/*`和`*/}}`来包含注释内容

##【变量】
```golang
 {{.}}
```
此标签输出当前对象的值
```golang
{{.Admpub}}
```
表示输出`Struct`对象中字段或方法名称为“Admpub”的值。

当“Admpub”是匿名字段时，可以访问其内部字段或方法,比如“Com”：`{{.Admpub.Com}}` ，

如果“Com”是一个方法并返回一个Struct对象，同样也可以访问其字段或方法：`{{.Admpub.Com.Field1}}`

<!--more-->

```golang
{{.Method1 "参数值1" "参数值2"}}
```
调用方法“Method1”，将后面的参数值依次传递给此方法，并输出其返回值。

```golang
 {{$admpub}}
```
此标签用于输出在模板中定义的名称为“admpub”的变量。当$admpub本身是一个Struct对象时，可访问其字段：`{{$admpub.Field1}}`

在模板中定义变量：变量名称用字母和数字组成，并带上“$”前缀，采用符号“:=”进行赋值。

比如：`{{$x := "OK"}}` 或 `{{$x := pipeline}}`

## 【管道函数】

用法1：

```golang
{{FuncName1}}
```
此标签将调用名称为“FuncName1”的模板函数（等同于执行“FuncName1()”，不传递任何参数）并输出其返回值。

用法2：
```
{{FuncName1 "参数值1" "参数值2"}}`
```
此标签将调用“FuncName1("参数值1", "参数值2")”，并输出其返回值

用法3：
```
{{.Admpub|FuncName1}}
```
此标签将调用名称为“FuncName1”的模板函数（等同于执行“FuncName1(this.Admpub)”，将竖线“|”左边的“.Admpub”变量值作为函数参数传送）并输出其返回值。

## 【条件判断】

用法1：
```
{{if pipeline}} T1 {{end}}
```
标签结构：`{{if ...}} ... {{end}}`

用法2：
```
{{if pipeline}} T1 {{else}} T0 {{end}}
```
标签结构：

```
{{if ...}} ... {{else}} ... {{end}}
```

用法3：
```
    {{if pipeline}} T1 {{else if pipeline}} T0 {{end}}
```
标签结构：`{{if ...}} ... {{else if ...}} ... {{end}}`

其中if后面可以是一个条件表达式（包括管道函数表达式。pipeline即管道），也可以是一个字符窜变量或布尔值变量。当为字符窜变量时，如为空字符串则判断为false，否则判断为true。

## 【遍历】

用法1：
```
{{range $k, $v := .Var}} {{$k}} => {{$v}} {{end}}
```
range...end结构内部如要使用外部的变量，比如.Var2，需要这样写：$.Var2

（即：在外部变量名称前加符号“$”即可，单独的“$”意义等同于global）

用法2：
```
{{range .Var}} {{.}} {{end}}
```
用法3：
```
{{range pipeline}} T1 {{else}} T0 {{end}}
```
当没有可遍历的值时，将执行else部分。

## 【嵌入子模板】

用法1：
```
{{template "name"}}
```
嵌入名称为“name”的子模板。使用前，请确保已经用`{{define "name"}}`子模板内容`{{end}}`定义好了子模板内容。

## 用法2：
```
{{template "name" pipeline}}
```
将管道的值赋给子模板中的“.”（即`{{.}}`）

## 【子模板嵌套】

```
{{define "T1"}}ONE{{end}}`
    
{{define "T2"}}TWO{{end}}`
    
    {{define "T3"}}{{template "T1"}} {{template "T2"}}{{end}}`
    
    {{template "T3"}}
```

*输出：*

```
ONE TWO
```
## 【定义局部变量】

用法1：
```
{{with pipeline}} T1 {{end}}
```
管道的值将赋给该标签内部的“.”。（注：这里的“内部”一词是指被`{{with pipeline}}...{{end}}`包围起来的部分，即T1所在位置）

用法2：

```
{{with pipeline}} T1 {{else}} T0 {{end}}
```
如果管道的值为空，“.”不受影响并且执行T0，否则，将管道的值赋给“.”并且执行T1。

说明：{{end}}标签是if、with、range的结束标签。


##【例子：输出字符窜】

```
{{"\"output\""}}
```
输出一个字符窜常量。

```
{{`"output"`}}
```
输出一个原始字符串常量
```
{{printf "%q" "output"}}
```
函数调用.（等同于：`printf("%q", "output")`。）
```
{{"output" | printf "%q"}}
```
竖线“|”左边的结果作为函数最后一个参数。（等同于：printf("%q", "output")。）
```
{{printf "%q" (print "out" "put")}}
```
圆括号中表达式的整体结果作为printf函数的参数。（等同于：`printf("%q", print("out", "put"))`。）
```
{{"put" | printf "%s%s" "out" | printf "%q"}}
```
一个更复杂的调用。（等同于：printf("%q", printf("%s%s", "out", "put"))。）
```
{{"output" | printf "%s" | printf "%q"}}
```
等同于：`printf("%q", printf("%s", "output"))`。
```
{{with "output"}}{{printf "%q" .}}{{end}}
```
一个使用点号“.”的with操作。（等同于：printf("%q", "output")。）
```
{{with $x := "output" | printf "%q"}}{{$x}}{{end}}
```
with结构，定义变量，值为执行管道函数之后的结果（等同于：$x := printf("%q", "output")。）

```
{{with $x := "output"}}{{printf "%q" $x}}{{end}}
```
with结构中，在其它动作中使用定义的变量
```
{{with $x := "output"}}{{$x | printf "%q"}}{{end}}
```
同上，但使用了管道。（等同于：printf("%q", "output")。）

 
===============【预定义的模板全局函数】================

【and】

```
{{and x y}}
```
表示：if x then y else x

如果x为真，返回y，否则返回x。等同于Golang中的：x && y

【call】
```
{{call .X.Y 1 2}}
```
表示：`dot.X.Y(1, 2)`

call后面的第一个参数的结果必须是一个函数（即这是一个函数类型的值），其余参数作为该函数的参数。

该函数必须返回一个或两个结果值，其中第二个结果值是error类型。

如果传递的参数与函数定义的不匹配或返回的error值不为nil，则停止执行。
 
【html】

转义文本中的html标签，如将“<”转义为“&lt;”，“>”转义为“&gt;”等
 
【index】

```
{{index x 1 2 3}}
```
返回index后面的第一个参数的某个索引对应的元素值，其余的参数为索引值

表示：`x[1][2][3]`

x必须是一个map、slice或数组

【js】

返回用JavaScript的escape处理后的文本

【len】

返回参数的长度值（int类型）

【not】

返回单一参数的布尔否定值。

【or】

```
{{or x y}}
```
表示：if x then x else y。等同于Golang中的：x || y

如果x为真返回x，否则返回y。

【print】

fmt.Sprint的别名

【printf】

fmt.Sprintf的别名


【println】

fmt.Sprintln的别名

【urlquery】

返回适合在URL查询中嵌入到形参中的文本转义值。（类似于PHP的urlencode）


=================【布尔函数】===============

布尔函数对于任何零值返回false，非零值返回true。

这里定义了一组二进制比较操作符函数：

【eq】

返回表达式“arg1 == arg2”的布尔值

【ne】

返回表达式“arg1 != arg2”的布尔值

【lt】

返回表达式“arg1 < arg2”的布尔值

【le】

返回表达式“arg1 <= arg2”的布尔值

【gt】

返回表达式“arg1 > arg2”的布尔值

【ge】

返回表达式“arg1 >= arg2”的布尔值

对于简单的多路相等测试，eq只接受两个参数进行比较，后面其它的参数将分别依次与第一个参数进行比较，
```
    {{eq arg1 arg2 arg3 arg4}}
```
即只能作如下比较：

arg1==arg2 || arg1==arg3 || arg1==arg4 ...
