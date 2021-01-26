---
title: "Golang-基于reflect和tag自动填充struct数据"
categories: [ "Golang" ]
tags: [ "golang","reflect" ]
draft: false
slug: "golang-automatically-populates-struct-data-based-on-reflect-and-tag"
date: "2017-10-22 15:10:00"
---

用Go开发Server端提供一些JSON数据格式的API，会定义业务Model，同时标记其json名字。

```
type School struct {
   ID   int    `json:"id"`
   Name string `json:"name"`
}
```

通常也会复用这个Model来接收创建或者更新请求的参数。


<!--more-->


```
var params map[string]interface{}
//parse parameters from request url path, queries or body
//...
s := &School{}
s.Name, _ = params["name"].(string)
//create school
//...
```

当School的字段很多时，上面的写法会非常烦人，我们可以利用json来简化一下

```
var s *School
d, _ := json.Marshal(params)
json.Unmarshal(d, &s)
```

> 这样做法的问题：Fields的in, out合在一起了，比如在创建School的请求中，ID字段是不能作为入参的，因此这里会有一些不妥。

思考：能不能效仿json tag，自定义一个param tag呢？

解决过程：

1. 定义基本类型和对外接口

```
type M map[string]interface{}
```

```
// 将m中的值赋给ptr指向的struct的相应字段
func (m M) AssignTo(ptr interface{}, tagName string) bool {
   v := reflect.ValueOf(ptr)
   if v.IsValid() == false {
      panic("not valid")
   }
```

```
   //找到最后指向的值，或者空指针，空指针是需要进行初始化的
   for v.Kind() == reflect.Ptr && !v.IsNil() {
      v = v.Elem()
   }

   tv := v
   if tv.Kind() == reflect.Ptr && tv.CanSet() {
      //对空指针进行初始化，暂时用临时变量保存
      tv.Set(reflect.New(tv.Type().Elem()))
      tv = tv.Elem()
   }

   if tv.Kind() != reflect.Struct {
      panic("not struct")
   }

   if assign(tv, m, tagName) { //赋值成功，将临时变量赋给原值
      if v.Kind() == reflect.Ptr {
         v.Set(tv.Addr())
      } else {
         v.Set(tv)
      }
      return true
   } else {
      return false
   }
}
```

\2. 实现赋值函数

```
//将src中的值填充到dstValue中
func assign(dstVal reflect.Value, src interface{}, tagName string) bool {
   sv := reflect.ValueOf(src)
   if !dstVal.IsValid() || !sv.IsValid() {
      return false
   }

   if dstVal.Kind() == reflect.Ptr {
      //初始化空指针
      if dstVal.IsNil() && dstVal.CanSet() {
         dstVal.Set(reflect.New(dstVal.Type().Elem()))
      }
      dstVal = dstVal.Elem()
   }

   // 判断可否赋值，小写字母开头的字段、常量等不可赋值
   if !dstVal.CanSet() {
      return false
   }

   switch dstVal.Kind() {
   case reflect.Bool: //TODO...
   case reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64: //TODO...
   case reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64: //TODO...
   case reflect.String: //TODO...
   case reflect.Slice://TODO...
   case reflect.Map://TODO...
   case reflect.Struct:
      if sv.Kind() != reflect.Map || sv.Type().Key().Kind() != reflect.String {
         return false
      }

      success := false
      for i := 0; i < dstVal.NumField(); i++ {
         fv := dstVal.Field(i)
         if fv.IsValid() == false || fv.CanSet() == false {
            continue
         }

         ft := dstVal.Type().Field(i)
         name := ft.Name
         strs := strings.Split(ft.Tag.Get(tagName), ",")
         if strs[0] == "-" { //处理ignore的标志
            continue
         }

         if len(strs[0]) > 0 {
            name = strs[0]
         }

         fsv := sv.MapIndex(reflect.ValueOf(name))
         if fsv.IsValid() {
            if fv.Kind() == reflect.Ptr && fv.IsNil() {
               pv := reflect.New(fv.Type().Elem())
               if assign(pv, fsv.Interface(), tagName) {
                  fv.Set(pv)
                  success = true
               }
            } else {
               if assign(fv, fsv.Interface(), tagName) {
                  success = true
               }
            }
         } else if ft.Anonymous {
            //尝试对匿名字段进行递归赋值，跟JSON的处理原则保持一致
            if fv.Kind() == reflect.Ptr && fv.IsNil() {
               pv := reflect.New(fv.Type().Elem())
               if assign(pv, src, tagName) {
                  fv.Set(pv)
                  success = true
               }
            } else {
               if assign(fv, src, tagName) {
                  success = true
               }
            }
         }
      }
      return success
   default:
      return false
   }

   return true
}
```

\3. 效果展示

```
type Room struct {
   ID   int    `param:"-" json:"id"`
   Name string `param:"name" json:"name"`
}

type School struct {
   ID     int    `param:"-" json:"id"`
   Name   string `param:"name" json:"name"`
   RoomID int    `param:"room_id" json:"-"`
   Room   *Room  `param:"-" json:"room"`
}

func main() {
   params := M{"id": "123", "name": "Primary School", "room_id": "1"}
   var s *School
   params.AssignTo(&s, "param")
   fmt.Println(s)
}
//output:
{0 Primary School 1 <nil>}
```

我们发现id字段其实没有赋值，其它字段赋值成功
原文： [Golang-基于reflect和tag自动填充struct数据](https://medium.com/@qiyutan/golang-%E5%9F%BA%E4%BA%8Ereflect%E5%92%8Ctag%E8%87%AA%E5%8A%A8%E5%A1%AB%E5%85%85struct%E6%95%B0%E6%8D%AE-508e9927d43)