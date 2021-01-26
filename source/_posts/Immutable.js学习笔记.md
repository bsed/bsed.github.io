---
title: "Immutable.js学习笔记"
categories: [ "JS" ]
tags: [ "javascript","immutable" ]
draft: false
slug: "immutablejs-study-notes"
date: "2016-08-28 21:30:00"
---

> 在 Immutable.js 的世界裡有一套属于它自己的规则，如果我们想要拥有它所提供的便利性，就必须先把我们熟知的阵列与物件等转换成它内部的资料结构来做更进一步的操作。由于 Immutable.js 有提供各式各样不同的结构，本文将只介绍较基本且常用到的 List，Map 与 Sequence。对于其他结构有兴趣的读者，请阅读[官方提供的说明页](http://facebook.github.io/immutable-js/docs/#/)。

### fromJS()

在介绍这些内部结构之前，我们先来瞭解一下该如何将 JavaScript 转换成 Immutable.js 可用的结构。转换的方式有很多，`fromJS()`是比较直接的一种。这个函式的主要功能就是将你丢给他的 JavaScript 物件或阵列转换成对应的 Immutable.js 结构。它的第二个参数则是一个 callback，让开发者能够自订转换出的最终结构，如不特别提供这个参数的话，预设会将阵列转换成 List，物件转换成 Map。

小提醒：`fromJS()`做的转换是深转换 (Deep Conversion)。


<!--more-->


### List

在 Immutable 的世界裡，List 就相当于 JavaScript 的阵列。我们可以透过 `List.of()` 建立一个 List。

```js
var list1= Immutable.List.of('a','b','c');
var list2 = list1.push('d'); // 'a','b','c','d'
console.log(list1.size); // 3
console.log(list2.size); // 4
```

如同上例所示，list1 并没有因为 `push()` 的关系改变了自已原本的结构，反而是生成了一个新的 List，而我们将它存为 list2。除此之外，许多 JavaScript 阵列原生方法例如 `shift()`，`unshift()`，`pop()` 等等，Immutable.js 都有实作对应的 API，让开发者可以无缝接轨的使用。

理解如何生成 List 后，让我们来看看如何取值与修改值吧。
如果 List 只有很单纯的一层时，使用 `get()` 与 `set()` :

```js
var list1 = Immutable.List.of(1,2,3,4);
var list2 = list1.set(1,5);
// list2 相当于 Immutable.List.of(1,5,3,4)
console.log(list2.get(1)); // 5
```
使用的方式很简单，就像阵列一样透过 index 的方式取值改值。

当 List 内部又有包其他 List 时，则需要使用 `setIn()` 与 `getIn()` :

```js
var list1 = Immutable.fromJS([1,[2,3],4]);
var list2 = list1.setIn([1,1],100);
// list2 相当于 Immutable.fromJS([1,[2,100],4])
console.log(list2.getIn([1,0])); // 2
console.log(list2.getIn([1,1])); // 100
```

`setIn()` 和 `getIn()` 的用法也很简单，阵列裡的数字其实就是每个层级的 Index。我们只要指到需要拿取或修改的 Index 就可以了。
上例在 `setIn()` 裡的 [1,1] 其实就是要取得 [1,[2,3],4] 裡的 [2,3] 裡的 3 并将它修改成 100。同理也可应用于取值。

### Map

Map 对应到的则是 JavaScript 中的物件。注意：物件并不等于是 Map，从 ES6 开始后，JavaScript 也有原生的 Map。建立 Map 与设值取值其实和 List 差不多:

```js
var map1 = Immutable.Map({js:'AngularJS',css:'Bootstrap'});
var map2 = map1.set('js','ReactJS');
console.log(map1.get('js')); // 'AngularJS'
console.log(map2.get('js')); // 'ReactJS'
```

深度取值与改值也是使用 `setIn()` 与 `getIn()` :

```js
var map1 = Immutable.fromJS({
    name: 'Howard',
    birthday: {
        year: 1988,
        month: 3,
        day: 28
    },
});
var map2 = map1.setIn(['birthday', 'year'], 2015);
console.log(map1.getIn(['birthday', 'year'])); // 1988
console.log(map2.getIn(['birthday', 'year'])); // 2015
```

### Sequence

Immutable.js 的设计灵感其实有一部分来自于 Clojure, Scala, Haskell 这些函数式编程语言。因此 Immutable.js 裡有个特殊的结构叫 Sequence。Map 和 List 都可以透过 `toSeq()` 这个方法来转换成 Sequence。 Sequence 有两个很重要的特性：

* Immutable (不可变)
* Lazy (延迟)

Immutable 我相信你应该很了解它的意思了，但延迟这特性是怎麽回事？我们直接来看看官方提供的例子好了：

```js
var oddSquares = Immutable.Seq.of(1,2,3,4,5,6,7,8)
  .filter(x => x % 2).map(x => x * x);
```

如果上面的例子是一般的 List 而不是 Sequence 的话，oddSquares 其实是等于 `[1,9,25,49]`。但因为 Sequence 拥有延迟的特性，在你要求它给你值之前他是不会把结果计算出来的。

```js
console.log(oddSquares.get(1)); // 9
```
当我们透过以上程式码向它取值时，oddSquares 才会把 9 给我们。特别注意的是，它也不会继续做后面 25 与 49 的运算。因为延迟的关系，Sequence 只会做到我们向它要求的地方。也因此，程式其实省去了很多不必要的运算，这也是为什麽 Immutable.js 会优化速度的原因之一。

为了理解，我们再来看一个官方提供的例子：

```js
Immutable.Range(1, Infinity)
  .skip(1000)
  .map(n => -n)
  .filter(n => n % 2 === 0)
  .take(2)
  .reduce((r, n) => r * n, 1);
// 1006008
```

Range 结构本身就是 Sequence，所以我们不需要特地使用 `toSeq()` 来做转换。在这个范例中，我们可以把它想像成是一个类似阵列的结构。

首先，建立一个类似 `[1,2,3...Infinity]` 的 Range。因为 Immutable 的关系，每一步其实都是一个新的 Range，但由于 Lazy 的特性，这些为了取值而在过程中建立的 Range 都不会被储存。

`skip(1000)` 的作用是跳过 Range 前 1000 个值。新的 Range 长这样：`[1001, 1002...Infinity]`。

接下来的 `map()` 和 `filter()` 会把新的 Range 过滤成 `[-1002, -1004, -1006...Infinity]`。

`take(2)` 则是取 Range 内最前面两笔资料。

最后再透过 `reduce()` 相乘得到 1006008。

以上这些运算如果不是透过 Sequence 延迟这个特性，在 `take(2)` 前的每一步都有大量的运算，电脑根本无法负荷。

## 总结

相信耐心读完本文的读者应该都对 Immutable.js 有了基本的了解。未来有机会的话也希望与各位分享如何将 Immutable.js 应用在 Flux 架构中。想要深入研究的读者也可以阅读[官方文件](http://facebook.github.io/immutable-js/docs/#/)或观看 Immutable.js 的开发者 Lee Byron 在 [2015 F8 的演讲](https://www.youtube.com/watch?v=I7IdS-PbEgI&list=PLb0IAmt7-GS1cbw4qonlQztYV1TAW0sCr&index=13)。
