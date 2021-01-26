---
title: "理解JavaScript中的Map [译文]"
categories: [ "JS" ]
tags: [ "javascript","map" ]
draft: false
slug: "understanding-map-in-jaxvascxript"
date: "2017-08-02 13:47:00"
---

# [Javascript Map from MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

The Map object is a simple key/value map. Any value (both objects and primitive values) may be used as either a key or a value.

### Syntax

```
new Map([iterable])
```

### Parameters

#### iterable

Iterable is an Array or other iterable object whose elements are key-value pairs (2-element Arrays). Each key-value pair is added to the new Map. null is treated as undefined.

可迭代表示元素是键值对形式的数组或可迭代的对象。每一个键值对将会被添加到新的Map中。`null`会被视为`undefined`

### Description

A Map object iterates its elements in insertion order — a for...of loop returns an array of [key, value] for each iteration.

Map对象按照元素插入的顺序迭代输出结果-`for...of`循环按照`[key, value]`的形式返回迭代的结果。


<!--more-->


### Key equality

Key equality is based on the "same-value" algorithm: NaN is considered the same as NaN (even though NaN !== NaN) and all other values are considered equal according to the semantics of the === operator. In earlier versions of the ECMAScript 6 draft -0 and +0 were considered distinct (even though -0 === +0), this has been changed in later versions and has been adapted in Gecko 29 (Firefox 29 / Thunderbird 29 / SeaMonkey 2.26) (bug 952870) and a recent nightly Chrome.

键相等基于"相同值"算法：NaN === NaN，其他值也是根据`===`运算符来确定是否相等。在老版本的ECMAScript 6草案中， -0和+0不认为是相等的（即使 -0 === +0），在后来版本中修正并被多数浏览器接受。

### Objects and maps compared

Objects are similar to Maps in that both let you set keys to values, retrieve those values, delete keys, and detect whether something is stored at a key. Because of this (and because there were no built-in alternatives), Objects have been used as Maps historically; however, there are important differences between Objects and Maps that make using a Map better:

* An Object has a prototype, so there are default keys in the map. This could be bypassed by using map = Object.create(null) since ES5, but was seldomly done.
* The keys of an Object are Strings and Symbols, where they can be any value for a Map.
* You can get the size of a Map easily while you have to manually keep track of size for an Object.

对象和Maps是很相似的，因为它们都可以通过key设置对应的值，获取值，删除key，检测是否存储对应的key。正因为此，早前一直使用对象来模拟Maps的功能；然而，对象和Maps在某些方面还是有很多差别：

* 对象有*prototype*, 所以对象中默认就有键值对。当然这在ES5可以通过使用`map = Object.create(null)`的方式绕开；
* 对象的键只能是*Strings*和*Symbols*,而Map可以使用任何的值作为键。
* 你可以很容易就获取到Map集合的大小，但获取对象的长度却并不容易。

This does not mean you should use Maps everywhere, objects still are used in most cases. Map instances are only useful for collections, and you should consider adapting your code where you have previously used objects for such. Objects shall be used as records, with fields and methods.
If you're still not sure which one to use, ask yourself the following questions:

* Are keys usually unknown until run time, do you need to look them up dynamically?
* Do all values have the same type, and can be used interchangeably?
* Do you need keys that aren't strings?
* Are key-value pairs often added or removed?
* Do you have an arbitrary (easily changing) amount of key-value pairs?
* Is the collection iterated?

Those all are signs that you want a Map for a collection. If in contrast you have a fixed amount of keys, operate on them individually, and distinguish between their usage, then you want an object.

这并不意味着任何时候都需要使用Maps，对象仍然是使用率最高的。Map实例通常用来处理集合，如果你的某些对象是用来处理这些工作，那么可以考虑使用Map。如果你还不确定，在使用Map之前问自己以下问题：

* key通常都是未知的，只要在运行的时候才能确定，并且需要动态的获取它们？
* 所有的值都是相同类型的，并且可以内部交换？ 
* 是否需要非字符串类型的键？
* 键值对是否经常被添加或者删除？ 
* 键值对的数量是随机的？
* 这个集合是需要被迭代？

这些都是需要使用Map集合来完成的表现。相反，如果你有固定的键，并且都是单独操作它们，那么你应该选择对象。


### Properties

#### Map.length
The value of the length property is 0.
#### get Map[@@species]
The constructor function that is used to create derived objects.

创建衍生对象的构造函数。
#### Map.prototype
Represents the prototype for the Map constructor. Allows the addition of properties to all Map objects.

代表Map构造函数的prototype.它给所有Map对象添加属性。

### Map instances
All Map instances inherit from Map.prototype.

所有的Map实例都继承自`Map.prototype`。
#### Properties

#### Map.prototype.constructor
Returns the function that created an instance's prototype. This is the Map function by default.

返回创建Map实例prototype的函数，默认就是Map函数。
### Map.prototype.size
Returns the number of key/value pairs in the Map object.

返回Map对象中的键值对的数量。 
### Methods

#### Map.prototype.clear()
Removes all key/value pairs from the Map object.

删除所有Map对象的键值对。
### Map.prototype.delete(key)
Removes any value associated to the key and returns the value that Map.prototype.has(key) would have previously returned. Map.prototype.has(key) will return false afterwards.

删除指定key对应的值，并且返回`Map.prototype.has(key)`对应的值，如果没有`Map.prototype.has(key)`将会返回默认的false。
#### Map.prototype.entries()
Returns a new Iterator object that contains an array of [key, value] for each element in the Map object in insertion order.

按照Map元素插入的顺序返回`[key, value]`形式的数组的Iterator对象
### Map.prototype.forEach(callbackFn[, thisArg])
Calls callbackFn once for each key-value pair present in the Map object, in insertion order. If a thisArg parameter is provided to forEach, it will be used as the this value for each callback.

按照插入的顺序对每个键值对调用*callbackFn*函数。如果提供*thisArg*参数，那么在在*callbackFn*函数中的`this`值将指向该参数。
#### Map.prototype.get(key)
Returns the value associated to the key, or undefined if there is none.

返回对应key的值，如果没有则会返回none。
### Map.prototype.has(key)
Returns a boolean asserting whether a value has been associated to the key in the Map object or not.

返回一个布尔值，判断Map中是否有key对应的值。
#### Map.prototype.keys()
Returns a new Iterator object that contains the keys for each element in the Map object in insertion order.
#### Map.prototype.set(key, value)
Sets the value for the key in the Map object. Returns the Map object.

给Map对象指定的键设置值。返回整个Map对象。
### Map.prototype.values()
Returns a new Iterator object that contains the values for each element in the Map object in insertion order.

返回包含每个Map元素值的新的Iterator对象
#### Map.prototype[@@iterator]()
Returns a new Iterator object that contains an array of [key, value] for each element in the Map object in insertion order.

Map对象的每个元素按照插入的顺序返回`[key, value]`形式数组的Iterator对象。
### Examples
#### Using the Map object

```
var myMap = new Map();

var keyString = "a string",
    keyObj = {},
    keyFunc = function () {};

// setting the values
myMap.set(keyString, "value associated with 'a string'");
myMap.set(keyObj, "value associated with keyObj");
myMap.set(keyFunc, "value associated with keyFunc");

myMap.size; // 3

// getting the values
myMap.get(keyString);    // "value associated with 'a string'"
myMap.get(keyObj);       // "value associated with keyObj"
myMap.get(keyFunc);      // "value associated with keyFunc"

myMap.get("a string");   // "value associated with 'a string'"
                         // because keyString === 'a string'
myMap.get({});           // undefined, because keyObj !== {}
myMap.get(function() {}) // undefined, because keyFunc !== function () {}
```

#### Using NaN as Map keys

NaN can also be used as a key. Even though every NaN is not equal to itself (NaN !== NaN is true), the following example works, because NaNs are indistinguishable from each other:

```
var myMap = new Map();
myMap.set(NaN, "not a number");

myMap.get(NaN); // "not a number"

var otherNaN = Number("foo");
myMap.get(otherNaN); // "not a number"
```

#### Iterating Maps with for..of

Maps can be iterated using a for..of loop:

```
var myMap = new Map();
myMap.set(0, "zero");
myMap.set(1, "one");
for (var [key, value] of myMap) {
  console.log(key + " = " + value);
}
// Will show 2 logs; first with "0 = zero" and second with "1 = one"

for (var key of myMap.keys()) {
  console.log(key);
}
// Will show 2 logs; first with "0" and second with "1"

for (var value of myMap.values()) {
  console.log(value);
}
// Will show 2 logs; first with "zero" and second with "one"

for (var [key, value] of myMap.entries()) {
  console.log(key + " = " + value);
}
// Will show 2 logs; first with "0 = zero" and second with "1 = one"
```
#### Iterating Maps with forEach()

Maps can be iterated using the forEach() method:

```
myMap.forEach(function(value, key) {
  console.log(key + " = " + value);
}, myMap)
// Will show 2 logs; first with "0 = zero" and second with "1 = one"
Relation with Array objects

var kvArray = [["key1", "value1"], ["key2", "value2"]];

// Use the regular Map constructor to transform a 2D key-value Array into a map
var myMap = new Map(kvArray);

myMap.get("key1"); // returns "value1"

// Use the spread operator to transform a map into a 2D key-value Array.
console.log(uneval([...myMap])); // Will show you exactly the same Array as kvArray

// Or use the spread operator on the keys or values iterator to get 
// an array of only the keys or values
console.log(uneval([...myMap.keys()])); // Will show ["key1", "key2"]
```
