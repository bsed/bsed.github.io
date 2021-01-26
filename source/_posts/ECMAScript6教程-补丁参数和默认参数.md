---
title: "ECMAScript6教程 补丁参数和默认参数"
categories: [ "JS" ]
tags: [ "ecmascript6","params" ]
draft: false
slug: "ecmascript6-tutorial-patches-and-default-arguments"
date: "2015-12-17 07:11:00"
---

## 不定参数

我们通常使用可变参函数来构造API，可变参函数可接受任意数量的参数。例如，String.prototype.concat方法就可以接受任意数量的字符串参数。ES6提供了一种编写可变参函数的新方式——不定参数。

我们通过一个简单的可变参数函数containsAll给大家演示不定参数的用法。函数containsAll可以检查一个字符串中是否包含若干个子串，例如：containsAll("banana", "b", "nan")返回true，containsAll("banana", "c", "nan")返回false。

首先使用传统方法来实现这个函数：
```javascript
    function containsAll(haystack) {
      for (var i = 1; i < arguments.length; i++) {
        var needle = arguments[i];
        if (haystack.indexOf(needle) === -1) {
          return false;
        }
      }
      return true;
    }
```
在这个实现中，我们用到了神奇的arguments对象，它是一个类数组对象，其中包含了传递给函数的所有参数。这段代码实现了我们的需求，但它的可读性却不是最理想的。函数的参数列表中只有一个参数haystack，我们无法一眼就看出这个函数实际上接受了多个参数。另外，我们一定要注意，应该从1开始迭代，而不是从0开始，因为arguments[0]相当于参数haystack。如果我们想要在haystack前后添加另一个参数，我们一定要记得更新循环体。不定参数恰好可以解决可读性与参数索引的问题。下面是用ES6不定参数特性实现的containsAll函数：


<!--more-->


```javascript
    function containsAll(haystack, ...needles) {
      for (var needle of needles) {
        if (haystack.indexOf(needle) === -1) {
          return false;
        }
      }
      return true;
    }
```

这一版containsAll函数与前者有相同的行为，但这一版中使用了一个特殊的...needles语法。我们来看一下调用containsAll("banana", "b", "nan")之后的函数调用过程，与之前一样，传递进来的第一个参数"banana"赋值给参数haystack，needles前的省略号表明它是一个不定参数，所有传递进来的其它参数都被放到一个数组中，赋值给变量needles。对于我们的调用示例而言，needles被赋值为["b", "nan"]，后续的函数执行过程一如往常。（注意啦，我们已经使用过ES6中for-of循环。）

在所有函数参数中，只有最后一个才可以被标记为不定参数。函数被调用时，不定参数前的所有参数都正常填充，任何“额外的”参数都被放进一个数组中并赋值给不定参数。如果没有额外的参数，不定参数就是一个空数组，它永远不会是`undefined`。

## 默认参数

通常来说，函数调用者不需要传递所有可能存在的参数，没有被传递的参数可由感知到的默认参数进行填充。JavaScript有严格的默认参数格式，未被传值的参数默认为undefined。ES6引入了一种新方式，可以指定任意参数的默认值。

```javasccipt
    function animalSentence(animals2="tigers", animals3="bears") {
        return `Lions and ${animals2} and ${animals3}! Oh my!`;
    }
```
默认参数的定义形式为[param1[ = defaultValue1 ][, ..., paramN[ = defaultValueN ]]]，对于每个参数而言，定义默认值时=后的部分是一个表达式，如果调用者没有传递相应参数，将使用该表达式的值作为参数默认值。相关示例如下：

```javascript
    animalSentence();                       // Lions and tigers and bears! Oh my!
    animalSentence("elephants");            // Lions and elephants and bears! Oh my!
    animalSentence("elephants", "whales");  // Lions and elephants and whales! Oh my!
```

默认参数有几个微妙的细节需要注意：

 - 默认值表达式在函数调用时自*左向右*求值，这一点与Python不同。这也意味着，默认表达式可以使用该参数之前已经填充好的其它参数值。举个例子，我们优化一下刚刚那个动物语句函数：

```javascript
    function animalSentenceFancy(animals2="tigers",
        animals3=(animals2 == "bears") ? "sealions" : "bears")
    {
      return `Lions and ${animals2} and ${animals3}! Oh my!`;
    }
```

现在，animalSentenceFancy("bears")将返回 “Lions and bears and sealions. Oh my!”。

 - 传递undefined值等效于不传值，所以animalSentence(undefined, "unicorns")将返回“Lions
   and tigers and unicorns! Oh my!”。
 - 没有默认值的参数隐式默认为undefined，

所以

`function myFunc(a=42, b) {...}`

是合法的，并且等效于

`function myFunc(a=42, b=undefined) {...}`

## 停止使用arguments

现在我们已经看到了`arguments对象`可被**不定参数**和**默认参数**完美代替，移除arguments后通常会使代码更易于阅读。除了破坏可读性外，众所周知，针对arguments对象对JavaScript虚拟机进行的优化会导致一些让你头疼不已的问题。

我们期待着不定参数和默认参数可以完全取代arguments，要实现这个目标，标准中增加了相应的限制：在使用不定参数或默认参数的函数中禁止使用arguments对象。曾经实现过arguments的引擎不会立即移除对它的支持，当然，现在更推荐使用不定参数和默认参数。

## 浏览器支持

Firefox早在第15版的时候就支持了不定参数和默认参数。

不幸的是，尚未有其它已发布的浏览器支持不定参数和默认参数。V8引擎最近增添了[针对不定参数的实验性的支持](https://code.google.com/p/v8/issues/detail?id=2159)，并且有一个开放状态的V8 [issue给实现默认参数使用](https://code.google.com/p/v8/issues/detail?id=2160)，JSC同样也有一个开放的issue来给[不定参数](https://bugs.webkit.org/show_bug.cgi?id=38408)和[默认参数](https://bugs.webkit.org/show_bug.cgi?id=38409)使用。

[Babel](http://babeljs.io/)和[Traceur](https://github.com/google/traceur-compiler#what-is-traceur)编译器都支持默认参数，所以从现在起就可以开始使用。