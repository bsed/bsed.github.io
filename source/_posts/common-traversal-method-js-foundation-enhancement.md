---
title: 常用遍历方法 [js基础增强]
date: 2019-05-17 13:09:00
updated: 2019-09-17 13:56:18
tags: 
- xrandr
categories: 
- linux

---
```
// array  object
const arr = [1, 2, 3, 4, 5]
const obj = {a:1, b:2, c:3}
```

### for 循环

```
for (let index = 0; index < arr.length; index++) {
  const element = arr[index]; // 1, 2, 3, 4, 5
}
```


<!--more-->


### forEach

```
arr.forEach((item, index) => {
  console.log(item, index) // 1,0  2,1  3,2  4,3  5,4
})
```

### map

```
arr.map((item, index) => {
  console.log(item, index) // 1,0  2,1  3,2  4,3  5,4
})
```

### filter 过滤 返回一个符合条件的数组

```
const newArr = arr.filter((item, index) => {
  console.log(item, index) // 1,0  2,1  3,2  4,3  5,4
  return item > 2
})
console.log(newArr)  // 3, 4, 5
```

### some 返回一个布尔值

某一个值满足条件返回true

```
const result = arr.some((item, index) => {
  return item > 3
})
console.log(result) // true
```

### every 返回一个布尔值

所有值满足条件返回true

```
const result2 = arr.every((item, index) => {
  return item > 2
})
console.log(result2) // false
```

### reduce array.reduce(callback[, initialValue]);

1. callback : 函数执行在数组中每个值
2. initialValue : 对象作为第一个参数回调的第一次调用使用

```
// 求和
var sum = arr.reduce(function(x, y) {
  return x + y
}, 0)
console.log(sum)  //  15 

// 取最大值
var max = arr.reduce(function(x, y) {
  return x > y ? x : y;
})
console.log(max) // 5
```

## for...in 与 for...of 使用与区别

- for...in es5标准 遍历key值 并且会遍历数组所有的可枚举属性 遍历顺序有可能不是按照实际数组的内部顺序 所以for in更适合遍历对象，不要使用for in遍历数组

```
for (index in arr) {
  console.log(index) // 0 1 2 3 4
}

for (index in obj) {
  console.log(index) // a b c
}
```

- for...of 是es6标准 遍历的是value值

```
for (item of arr) {
  console.log(item) // 0 1 2 3 4
}
```

### ES6-对象的扩展-Object.keys()，Object.values()，Object.entries()

- Object.keys() ES5 引入了 Object.keys 方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```
Object.keys(obj) // [a, b, c]
```

- Object.values 方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。 返回数组的成员顺序是按照key值的大小顺序排列(升序)

```
Object.values(obj)  // [1, 2, 3]
```

- Object.entries 返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。如果原对象的属性名是一个 Symbol 值，该属性会被忽略。

```
Object.entries(obj)  // [['a': 1],['b': 2], ['c': 3]]
```

