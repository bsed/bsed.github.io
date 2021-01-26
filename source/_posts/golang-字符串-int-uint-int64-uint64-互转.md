---
title: "golang 字符串 int uint int64 uint64 互转"
categories: [ "Golang" ]
tags: [ "golang" ]
draft: false
slug: "golang-string-int-uint-int64-uint64"
date: "2020-09-05 20:57:00"
---

golang 字符串 int uint int64 uint64 互转

## 字符串 转 int
```
intNum, _ = strconv.Atoi(intStr)
```

## 字符串 转 int64
```
intNum, _ := strconv.Atoi(intStr)
int64Num = int64(intNum)
 ```


<!--more-->


## 字符串 转 uint64
```
intNum, _ := strconv.Atoi(intStr)
int64Num = uint64(intNum)
```
## int 转 字符串
```
intStr = strconv.Itoa(intNum)
```
## int64 转 字符串
```
int64Str = strconv.FormatInt(intNum, 10)
```

## uint64 转 字符串
```
int64Str = strconv.FormatUint(intNum, 10)
```