---
title: JS下实现多维数组转换为一维数组的几种方法
date: 2019-12-18 11:01:00
updated: 2020-01-16 13:29:41
tags: 
- windows
- conf
- mpv
categories: 
- linux

---
> 下面介绍多维数组转一维数组的方法

![javascript_array_to_string_01.png][1]

## 转字符串法

tempArr是一个多维数组，先使用`join`方法将其转为字符串，然后使用`split`方法转换为一维数组。

缺点： 如果数组中的各个项是number，将不可避免的转为字符串。


<!--more-->


```javascript
let tempArr = [1, ["a32", 3], [4, 5, [6, 7]]];
let result1 = tempArr.join(",").split(",");
console.log(result1); //  ["1", "2", "3", "4", "5", "6", "7"]
```

优化：

如果数组中各个项都是number类型或string类型时，可以使用了map进行再次遍历。

```javascript
 function mapNumber(arr) {
  let str1 = arr.join(",").split(",");
  let result_number = str1.map(item => {
    return Number(item);
  });
  return result_number;
}
console.log(mapNumber(tempArr)); // [1, 2, 3, 4, 5, 6, 7]

function mapStr(arr) {
  let str1 = arr.join(",").split(",");
  let result_number = str1.map(item => {
    return String(item);
  });
  return result_number;
}
let tempArr1 = [1, ["a32", 3], [4, 5, [6, 7]]];
console.log(mapStr(tempArr1)); // ["1", "a32", "3", "4", "5", "6", "7"]
```
## 使用递归
使用·Object.prototype.toString.call() 或者 `Array.isArray`来判断array的类型

```javascript
let result = [],
  tempArr6 = ["1", "2", ["3", "4", [5, 6]], 7];
  function recursive(arr) {
    for (let item of arr) {
      if (Object.prototype.toString.call(item).slice(8, -1) === "Array") {
        recursive(item);
      } else {
        result.push(item);
      }
    }
    return result;
  }
  console.log(recursive(tempArr6)); // ["1", "2", "3", "4", 5, 6, 7]
```

##  concat，apply  方法

**concat**方法参数：会把一维数组转为单个的项和前面的数组连接起来，如果是多维数组，则保留多维数组里面的项。

```javascript
let tempArr3 = ['3',4,5,'6'],tempArr4 = ['3',4,[5,6]];
console.log([1,2].concat(tempArr3)); // [1, 2, "3", 4, 5, "6"]
console.log([1,2,].concat(tempArr4)); // [1, 2, "3", 4, Array(2)]
```
使用concat方法只能把二维数组转为一维数组

```javascript
let tempArr5 = [[1,2],'3',4,[5,6],[7]];
let temparr6 = [].concat.apply([],tempArr5);
console.log(temparr6); // [1, 2, "3", 4, 5, 6, 7]
```


  [1]: https://imgs.gnux.cn/usr/uploads/2019/12/2393277859.png