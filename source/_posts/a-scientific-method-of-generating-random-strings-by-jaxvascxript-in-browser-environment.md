---
title: 浏览器环境下 JavaScript 生成随机字符串的科学方法
date: 2018-02-26 08:57:00
updated: 2018-02-27 15:36:23
tags: 
- python
- win32api
categories: 
- python

---
## 随机数与随机字符串

早前我都使用`Math.random()`来生成随机数，然后转成16进制字符串来生成随机字符串，后来看到了[这个回答（stackoverflow）](https://stackoverflow.com/questions/9407892/how-to-generate-random-sha1-hash-to-use-as-id-in-node-js/14869745#14869745)。 `Math.random()` 确实不是一个好的选择，因此我需要更加科学的方法。

## window.crypto (Web Cryptography API)

[Web Cryptography API 已经是 W3C Recommendation 级别](https://www.w3.org/TR/WebCryptoAPI)，常见浏览器的近期版本也都已经实现了。 ![0ivS.png][1]使用的时候也可以不用担心兼容性问题。


<!--more-->


## crypto.getRandomValues

`crypto.getRandomValues` 是我们用来实现随机字符串的主要方法，这个方法从实现和随机性的角度来说，更加高效、可靠。 10.2.1. The getRandomValues method The getRandomValues method generates cryptographically random values. It must act as follows:

1. If array is not of an integer type (i.e., Int8Array, Uint8Array, Int16Array, Uint16Array, Int32Array, Uint32Array or UInt8ClampedArray), throw a TypeMismatchError and terminate the algorithm.
2. If the byteLength of array is greater than 65536, throw a QuotaExceededError and terminate the algorithm.
3. Overwrite all elements of array with cryptographically random values of the appropriate type.
4. Return array.

> Note Do not generate keys using the getRandomValues method. Use the generateKey method instead.

使用方法就是传入一个 Int 的数组，然后返回被加密之后的数组（覆盖原始数组）。

## 科学的随机字符串生成方法

知道了`crypto.getRandomValues`的使用方法之后，我们就可以来实现一个科学的随机字符串生成方法了。 首先毫无疑问我们需要一个 UintArray，这比我们手动生成随机数的数组要简单多了。

```
let len = 64;
const arr = new Uint8Array(len / 2);
```

假设我们要生成长度为 64 的随机字符串，len 就是 64，之所以在生成 Uint8Array 时候，要将长度除以2，是因为最终我们是通过16进制字符串的形式来输出的，并且在头部补0取末尾两位，所以只需要一半长度的 Uint8Array 就可以了。 用`crypto.getRandomValues()`加密这个数据

```
window.crypto.getRandomValues(arr);
```

把这个 Uint8Array 转换成普通数组，并且把每一个值转换成16进制字符串（首位补0），取末尾2位，最后拼接到一起，就是最终的随机字符串了。

```
// dec2hex :: Integer -> String
function dec2hex (dec) {
  return ('0' + dec.toString(16)).substr(-2);
}
    
// generateId :: Integer -> String
function generateId (len = 40) {
  const arr = new Uint8Array(len / 2);
  window.crypto.getRandomValues(arr);
  return Array.from(arr, dec2hex).join('');
}
```

使用这个方法来生成随机字符串，更加高效、科学、可靠。 gist: [Generate random string/characters in JavaScript](https://gist.github.com/gucheen/f6f302f090e7581b2b2cf22faa125a44)

原文：[https://blog.guchengf.me/better-way-to-generate-random-bytes-in-browser](https://blog.guchengf.me/better-way-to-generate-random-bytes-in-browser)


  [1]: https://imgs.gnux.cn/usr/uploads/2018/02/1778616155.png