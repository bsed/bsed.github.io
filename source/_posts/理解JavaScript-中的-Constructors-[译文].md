---
title: "理解JavaScript 中的 Constructors [译文]"
categories: [ "JS" ]
tags: [ "javascript","constructors" ]
draft: false
slug: "understanding-constructorstranslation-in-jaxvascxript"
date: "2017-08-02 13:37:00"
---

## [Understanding JavaScript Constructors](https://css-tricks.com/understanding-javascript-constructors/)
SEPTEMBER 24, 2015

  The following is a guest post by Faraz Kelhini. Some of this stuff is out of my comfort zone, so I asked Kyle Simpson to tech check it for me. Kyle's answer (which we did during an Office Hours session) was very interesting. It was: 1) This article is technically sound. JavaScript doesn't really have classes in a traditional sense and this is the way most people shoehorn them in. 2) We may want to stop shoehorning them in. JavaScript has objects and we can use them in the way they are intended to do the same kinds of things. Kyle calls it OLOO (Objects Linked to Other Objects). Here's an intro. I'd think there is value in learning about both.


  Having a good understanding of constructors is crucial to truly understand the JavaScript language. Unlike many other languages, JavaScript doesn't support classes, but it has constructors to bring similar functionality to JavaScript. In this tutorial, we will explore constructors in detail and see how JavaScript utilizes them to make objects.
Constructors are like regular functions, but we use them with the "new" keyword. There are two types of constructors: native (aka built-in) constructors like Array and Object, which are available automatically in the execution environment at runtime; and custom constructors, which define properties and methods for your own type of object.

  理解构造函数对于真正理解JavaScript这门语言是至关重要的。不像其他的编程语言，JavaScript不支持"类"，但是它的构造函数给我们带来和"类"相似的功能特性。在这篇文章中，我们将详细介绍JavaScript的构造函数，看看它是如何使用构造函数创建“类”的。构造函数和普通的JavaScript函数类似，唯一的不同点是需要使用`new`关键字。在JavaScript中，有两类构造函数：像Array，Object这样的内置构造函数，我们可以在执行环境中直接使用；另一类是自定义的构造函数，它自定义对象的属性和方法。

  A constructor is useful when you want to create multiple similar objects with the same properties and methods. It’s a convention to capitalize the name of constructors to distinguish them from regular functions. Consider the following code:

  当你想同时创建多个具有相同属性和方法的对象时非常有用。我们使用首字母大写的方式来定义将构造函数和普通函数区分。看下面的代码：


<!--more-->


```
function Book() { 
  // unfinished code
} 
var myBook = new Book();
```

  The last line of the code creates an instance of Book and assigns it to a variable; even though the Book constructor doesn't do anything, myBook is still an instance of it. As you can see there is no difference between this function and regular functions except that we call it with the new keyword and the function name is capitalized.

  最后一行代码创建了一个`Book`的实例，并将其赋值给一个变量；即使该`Book`构造函数并没有做任何事情，`myBook`依然是它的一个实例。你可以看到该构造函数和普通函数并没有任何的差别，除了函数名首字母大写，并且使用`new`关键字来调用它。

### Determining the Type of an Instance

To find out whether an object is an instance of another one, we use the instanceof operator:

判断一个对象是否构造函数的实例，我们使用`instanceof`操作符：

```
myBook instanceof Book    // true
myBook instanceof String  // false
Note that if the right side of the instanceof operator isn’t a function, it will throw an error:

myBook instanceof {};
// TypeError: invalid 'instanceof' operand ({})
```

Another way to find the type of an instance is using the constructor property. All object instances have a constructor property that point to the constructor function that created it.

Consider the following code fragment:

获取对象类型的另外一种方法就是使用`constructor`属性。所有的对象实例都有一个`constructor`的属性，并且`constructor`属性指向了创建该对象实例的构造方法。

```
myBook.constructor == Book;   // true
```

Since the constructor property of myBook points to Book the result is true. All objects inherit a constructor property from their prototype:

上面的代码返回结果为真。这表示所有使用该构造函数创建的对象都从它们的`prototype`中继承了`constructor`属性。

```javascript
var s = new String("text");
s.constructor === String;      // true

"text".constructor === String; // true

var o = new Object();
o.constructor === Object;      // true

var o = {};
o.constructor === Object;      // true

var a = new Array();
a.constructor === Array;       // true

[].constructor === Array;      // true
```

Although checking constructor property can be used to check the type of an instance, it is recommended to only use the instanceof operator for this purpose, because the constructor property might be overwritten, so it cannot be a reliable method for checking the type of an instance.

  尽管可以使用检测`constructor`的方法来判断对象实例的类型，我们建议只使用`instanceof`操作符来完成这个目的，因为`constructor`属性可能会被重写，所以它不可以作为判断对象实例类型的可靠方法。

### Custom Constructor Functions

A constructor is like a cookie cutter to make more than one object with similar features. In other words, the benefit of using a constructor is that it makes it easy to create multiple objects with the same properties and methods.

  构造函数有点类似于饼干分割机，它使得创建多个具有相似属性对象变得非常简单。换句话说，他可以创建出多个具有相同属性，方法的相似对象。

Consider the following code:

```
function Book(name, year) {
  this.name = name;
  this.year = '(' + year + ')';
}
```

The book constructor expects two parameters: name and year; when it is called with the new keyword it assigns the received parameters to the name and year property of the current instance so the constructor can be used to create objects with initialized name and year properties:

  `Book`构造函数接收两个参数： `name`和`year`；当使用`new`关键字调用该构造函数时，它将参数指定到当前对象实例的`name`和`year`属性。

```
var firstBook = new Book("Pro AngularJS", 2014);
var secondBook = new Book("Secrets Of The JavaScript Ninja", 2013); 
var thirdBook = new Book("JavaScript Patterns", 2010); 
console.log(firstBook.name, firstBook.year);           
console.log(secondBook.name, secondBook.year);           
console.log(thirdBook.name, thirdBook.year); 
```

This code logs the following in the console:
![a](https://cdn.css-tricks.com/wp-content/uploads/2015/09/console-log.png)


As you can see, we can quickly build a large number of different book objects by invoking the Book constructor with different arguments. This is exactly the same pattern that JavaScript uses in its built-in constructors like Array() and Date().

上面的代码中，我们通过调用`Book`构造函数，并传入不同的参数值创建了大量`Book`对象实例。这和使用`Array()`，`Date()`这些内种构造函数的方法是一样的。

### Object.defineProperty Function

The Object.defineProperty() can be used inside of a constructor to help perform all necessary property setup. Consider the following constructor:

可以在构造函数内部使用`Object.defineProperty()`来完成必要的属性初始化。参考下面的代码：

```
function Book(name) { 
  Object.defineProperty(this, "name", { 
      get: function() { 
        return "Book: " + name;       
      },        
      set: function(newName) {            
        name = newName;        
      },               
      configurable: false     
   }); 
}

var myBook = new Book("Single Page Web Applications");
console.log(myBook.name);    // Book: Single Page Web Applications

// we cannot delete the name property because "configurable" is set to false
delete myBook.name;    
console.log(myBook.name);    // Book: Single Page Web Applications

// but we can change the value of the name property
myBook.name = "Testable JavaScript";
console.log(myBook.name);    // Book: Testable JavaScript
```

In this code we used accessor properties inside the Object.defineProperty(). Accessor properties don’t include any properties or methods, but they define a getter to call when the property is read, and a setter to call when the property is written to.

A getter is expected to return a value, while a setter receives the value being assigned to the property as an argument. This constructor allows us to set or change the name property of instances, but we are not allowed to delete it, and when we get the value of name, the getter prepends the string "Book: " to the name and returns it.

这里我们在`Object.defineProperty()`里面使用了访问器属性。访问器属性不再包含其他属性或方法，但是定义了*getter*方法获取属性值，*setter*方法将name的内容添加到"Book:"字符串的后面。 

### Object Literal Notations are Preferred to Constructors

The JavaScript language has nine built-in constructors: Object(), Array(), String(), Number(), Boolean(), Date(), Function(), Error() and RegExp(). When creating values we are free to use either object literals or constructors, but object literals are not only easier to read but also faster to run because they can be optimize at parse time. Whenever we need simple objects it's best to stick with literals:

  JavaScript语言有9个内置的构造函数：`Object()`, `Array()`, `String()`, `Number()`, `Boolean()`, `Date()`, `Function()`, `Error()`, `RegExp()`。虽然在创建对象时使用对象字面量和构造方法都是可以的，但是对象字面量更简单也更快，因为它们实在允许的时候被编译的。

```
// a number object
// numbers have a toFixed() method
var obj = new Object(5);
obj.toFixed(2);     // 5.00

// we can achieve the same result using literals
var num = 5;
num.toFixed(2);     // 5.00

// a string object
// strings have a slice() method 
var obj = new String("text");
obj.slice(0,2);     // "te"

// same as above
var string = "text";
string.slice(0,2);  // "te"
```

As you can see there's hardly any difference between these object literals and constructors and we can still call methods on literals. It's because when we call a method on a literal, behind the scene JavaScript converts the literal to a temporary object so that it's possible to use object methods for primitive values, then JavaScript discards the temporary object.

  你可以看到使用对象字面量和构造函数创建对象几乎是没有差别的，而且我们依然能够在对象字面量上调用方法。当我们在对象字面量上调用方法时，JavaScript首先将字面量转化为一个临时的对象， 这样你就可以使用对象方法，然后JavaScript再丢弃这个临时对象。 

### Using the "new" Keyword is Essential

It's important to remember to use the new keyword before all constructors, if we accidentally forget "new" we will be modifying the global object instead of the newly created object. Consider the following example:

一定要记住在构造函数之前使用`new`关键字来创建对象实例。如果我们忘记使用`new`关键字，那么我们其实是在对全局的global对象做修改，而不是当前的对象实例。参考下面的代码：

```
function Book(name, year) {
  console.log(this);
  this.name = name;
  this.year = year;
}

var myBook = Book("js book", 2014);  
console.log(myBook instanceof Book);  
console.log(window.name, window.year);

var myBook = new Book("js book", 2014);  
console.log(myBook instanceof Book);  
console.log(myBook.name, myBook.year);
```

When we call the Book constructor without new keyword, in fact we are just calling a function without a return statement and "this" inside the Book constructor will be equal to Window (instead of myBook), in other words we have unintentionally created two global variables which causes the code to produce unintended results, but when we call the function with the "new" keyword the context is switched from global (window) to the instance, therefore "this" points to myBook.

  当我们忘记使用`new`关键字来调用`Book`构造函数时，事实上我们只是在调用一个没有返回值的函数，在构造函数中的"this"关键字会指向全局的"window"对象。换句话说，我们无意之间就创建了两个全局的变量，它有可能会给系统带来潜在的风险。当我们使用`new`关键字时，构造函数中的"this"就指向了对象实例而不是全局的window对象。

Here is what the above code logs in browser console:

![abc](https://cdn.css-tricks.com/wp-content/uploads/2015/09/console-log-2.png)

Note that in strict mode this code throws an error because strict mode protects us from accidentally calling a constructor without the new keyword.

注意如果是在"strict"模式下，这段代码将会抛出错误，因为"strict"模式要求在调用构造函数时必须使用`new`关键字。

### Scope-Safe Constructors

Since a constructor is just a function, it can be called without the new keyword, but this leads to unexpected results and errors especially for inexperienced programmers. The solution to this problem is scope-safe constructors. We can call Scope-safe constructors with or without new keyword and they return the same result in either form.

Most of built-in constructors, such as Object, Regex and Array, are scope-safe. They use a pattern to determine whether the constructor is called with new or not.

If new isn't used, a proper instance of the object is created by calling the constructor again with new keyword. 

因为构造函数也只是一个函数，它可以在不使用`new`关键字的情况下被调用，它可能会导致不可预知的结果或者错误。解决方法就是作用域安全(scope-safe)。我们可以选择是否使用`new`关键字来创建构造函数，它都能得到相同的结果

Consider the following code:

```
function Book(name) { 
  if (!(this instanceof Book)) { 
    
    // the constructor was called without "new".
    return new Book(name);
  } 
}
```

So using this pattern we can easily rewrite a scope-safe version of our constructor:

```
function Book(name, year) { 
  if (!(this instanceof Book)) { 
    return new Book(name, year);
  }
  this.name = name;
  this.year = year;
}

var person1 = new Book("js book", 2014);
var person2 = Book("js book", 2014);
console.log(person1 instanceof Book);    // true
console.log(person2 instanceof Book);    // true
```

The fact that "this" is an instance of the custom type allows us to determine if new is not used as soon as the constructor begins to execute and run the constructor again with new keyword.

"this"是自定义对象实例的一个引用，它让我们能够在调用构造函数时就能马上知道是否使用了`new`关键字， 如果没有使用则使用`new`关键字重新调用一遍构造函数。

### Conclusion

JavaScript has no class statement, which is baffling for coders who are used to languages with a class statement; However, JavaScript has constructors to bring similar functionality. Constructors are just regular functions which are used with new keyword. They come in handy when we need to make multiple similar objects with the same properties and methods.

  JavaScript没有"class"这种语法，这让那些习惯使用有"class"语言的程序员感到困惑；然而构造函数带来了和"class"类似的功能特性。构造函数就是当我们需要使用多个具有相同属性，相同方法的对象实例时，使用`new`关键字来调用的函数。