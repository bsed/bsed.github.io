---
title: "如何判断一个数值类型 [JS基础增强]"
categories: [ "JS" ]
tags: [ "Number.isInteger","数值类型" ]
draft: false
slug: "how-to-judge-a-numerical-type-js-basic-enhancement"
date: "2019-12-23 09:37:00"
---

## 判断整数 ES6

`Number.isInteger()`  先判断该值是否为number类型，不是直接返回false；是number类型的话再判断是否为整数。

```javascript
Number.isInteger(100); //true
Number.isInteger(100.001); //false
Number.isInteger('100'); // false
Number.isInteger('100.001'); //false
Number.isInteger('foo'); // false
```


<!--more-->


如图：
![mbioq_number_is_integer_01.png][1]

<!--more-->


## Math.round 四舍五入 ES5

利用四舍五入来判断该值是否为整数。


```javascript
function numIsInteger(n){
    if(!Number.isInteger){
        return typeof n === 'number' && Math.round(n) === n;
    }
    return n;
}
```

## 利取余

```javascript
function numIsInteger(n){
    if(!Number.isInteger){
        return typeof n === 'number' && n % 1 === 0;
    }
    return Number.isInteger(n);
}
```

## Math.sign() ES6
返回 正数（+1）,负数（-1），0 （0），-0（-0），其他值（NaN）

```javascript
console.log(Math.sign(-100)); //-1
console.log(Math.sign(-100.001)); // -1
console.log(Math.sign(100)); // 1
console.log(Math.sign(0)); // 0
console.log(Math.sign(-0)); // -0
console.log(Math.sign('foo')); // NaN
```
## Math.sign() ES5

```javascript
Math.sign = Math.sign || function (n){
    n = +n;
    if(n === 0 || isNaN(n)){
        return n;
    }
    return x > 0 ? 1 : -1;
}
```


  [1]: https://imgs.gnux.cn/usr/uploads/2019/12/3151831620.png