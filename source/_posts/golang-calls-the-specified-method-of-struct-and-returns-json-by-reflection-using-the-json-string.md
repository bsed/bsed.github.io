---
title: golang通过反射使用json字符串
date: 2017-07-13 09:27:00
updated: 2017-07-14 09:27:29
tags: 
- css
categories: 
- css

---
## 起因

　　在很多场合会存在这样一个需求或者想法：提交一个类似这样的json

```
{
    "func_name":"FooBarAdd",
    "params":[
        123.4,
        432.1
    ]
}
```

然后得到一个这样的json

```
{
    "func_name":"FooBarAdd",
    "data":[
        555.5
    ]
}
```


<!--more-->


　　要达到这样的目的，必须要先解析提交的json, 获取到方法名和参数，然后找到拥有这个方法的实体，将参数做适当的类型转换后调用方法，最后将得到的结果包装成json返回。
　　至于这个json是通过http提交与返回，还是通过websocket提交与返回，抑或是通过tcp直接提交与返回，都是可以的。下面来看一下如何达到这个目的。

## 通过反射获取struct指定的方法

　　假设现在有一个struct是这样的

```
type FooBar struct {
}

func (f *FooBar) FooBarAdd(argOne, argTwo float64) float64 {

    return argOne + argTwo
}
```

对于BarFuncAdd方法，可以这样调用：

```
foobar := &FooBar{}
resultCallByName :=reflect.ValueOf(foobar).MethodByName("FooBarAdd").
    Call([]reflect.Value{reflect.ValueOf(123.4),reflect.ValueOf(432.1)})

fmt.Println(resultCallByName[0].Float())
```

也可以这样调用：

```
foobar := &FooBar{}
resultCallByIndex :=reflect.ValueOf(foobar).Method(0).
    Call([]reflect.Value{reflect.ValueOf(123.4),reflect.ValueOf(432.1)})

fmt.Println(resultCallByIndex[0].Float())
```

结果都是555.5

## 通过json字符串调用

　　如果想用一串这样的json

```
{
    "func_name":"FooBarAdd",
    "params":[
        123.4,
        432.1
    ]
}
```

得到一个这样的结果

```
{
    "func_name":"FooBarAdd",
    "data":[
        555.5
    ]
}
```

则可以这样调用

```
foobar := &FooBar{}

jsonData := `
                {
                    "func_name":"FooBarAdd",
                    "params":[
                        123.4,
                        432.1
                    ]
                }
`
type RequestInfo struct {
    FuncName string        `json:"func_name"`
    Params   []interface{} `json:"params"`
}

type ResultInfo struct {
    FuncName string        `json:"func_name"`
    Data     []interface{} `json:"data"`
}

requestInfo := &RequestInfo{}

json.Unmarshal([]byte(jsonData), &requestInfo)

resultCallByJson :=reflect.ValueOf(foobar).MethodByName(requestInfo.FuncName).
    Call([]reflect.Value{reflect.ValueOf(requestInfo.Params[0]),
    reflect.ValueOf(requestInfo.Params[1])})

result := &ResultInfo{FuncName:requestInfo.FuncName,
    Data: []interface{{resultCallByJson[0].Interface()}}

jsonResult, _ := json.Marshal(&result)
fmt.Printf("%s\n", jsonResult)
```

## 适当的完善

　　是不是觉得还少了点什么，比如要是参数个数不匹配怎么办？类型转化怎么处理？如何做到更通用比如像这样调用：

```
jsonDataBarFuncSwap := `
{
    "func_name":"BarSwap",
    "params":[
        0.1,
        0.9
    ]
}
`

result:=string(reflectinvoke.InvokeByJson([]byte(jsonStr))
```
