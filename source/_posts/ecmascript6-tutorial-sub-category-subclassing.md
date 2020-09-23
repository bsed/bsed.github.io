---
title: ECMAScript6教程 子类 Subclassing
date: 2015-12-26 16:19:00
updated: 2016-01-24 16:47:41
tags: 
- java
- jstl
categories: 
- java

---
ES6的类系统借鉴了传统的C++和Java中的类系统，它们都支持类继承，子类可以继承父类，并在其基础上扩展更多自己的功能。那就让我们继续今天的文章，进一步发掘这个新特性无穷的潜力吧。

在我们开始讨论子类的概念之前，还是再一起巩固一下属性继承和动态原型链的相关知识。

## JavaScript继承
我们在创建对象的时候可以为其添加各种属性，但在这个过程中，新创建的对象同时也继承了原型对象的属性。作为JavaScript开发者，你应该很熟悉Object.create这个API，我们可以用它来创建一些新对象：
```javascript
    var proto = {
        value: 4,
        method() { return 14; }
    }
    var obj = Object.create(proto);
    obj.value; // 4
    obj.method(); // 14
```
进一步说，如果我们在创建obj时给它添加proto已有的属性，则新创建对象会覆盖原型对象中的同名属性。
```javascript
    obj.value = 5;
    obj.value; // 5
    proto.value; // 4
```
## 子类化的基本概念


<!--more-->


现在我们已经了解，通过类创建的对象，它们的原型链之间是如何连接的。回想一下，当我们创建一个类的时候，类的定义中有一个constructor方法，这个方法控制着所有静态方法，实际上我们正是依据这个方法创建了一个新的函数；然后我们又创建一个对象并将它赋给这个函数的prototype属性。为了使新创建的类继承所有的静态属性，我们需要让这个新的函数对象继承超类的函数对象；同样，为了使新创建的类继承所有实例方法，我们需要让新函数的prototype对象继承超类的prototype对象。

看官您若要是感觉这长篇大论看得头疼，不妨来看一段简单的示例，看我们如何通过旧语法的力量将这一切连结起来，然后再进一步美化这段代码的结构。

继续我们之前的示例，假设我们有一个类Shape，并且想要基于这个类生成一个子类：
```javascript
   class Shape {
        get color() {
            return this._color;
        }
        set color(c) {
            this._color = parseColorAsRGB(c);
            this.markChanged();  // 稍后重绘Canvas
        }
    }
```
在尝试编写这些代码时，我们仍会面临以前遇到过的static属性的问题：我们不能在定义函数的同时改变它的原型，到目前为止我们所知的语法不能实现这种功能。只不过你可以通过Object.setPrototypeOf方法绕过这一限制，但随即带来的问题是，这个方法性能很差，而JS引擎又很难对其进行优化，所以我们期待一种更完美的方法，能够在创建函数的同时处理好原型。
```javascript
    class Circle {
        // 与上文中代码相同
    }
    // 连结实例属性
    Object.setPrototypeOf(Circle.prototype, Shape.prototype);
    // 连结静态属性
    Object.setPrototypeOf(Circle, Shape);
```
这 段代码丑爆了！我们为JavaScript添加类语法的初衷是：能够用一段完整的代码封装目标对象的所有逻辑，而不是在类声明结束后再进行一些额外的“连 结”操作。Java、Ruby和其它面向对象的语言中有一种特定的语法可以声明“一个类是另一个的子类”，JavaScript中当然也要有这样的方法！ 我们可以用关键词extends声明子类关系，看好了，代码可以这样去写：
```javascript
    class Circle extends Shape {
        // 与上文中代码相同
    }
```
extends后面可以接任意合法且拥有prototype属性的构造函数。它可以是：

 - 另一个类
 - 源自现有继承框架（译者注：作者指的是原型继承，即使在JavaScript中类继承的本质也是原型继承）的近类函数
 - 一个普通的函数
 - 一个包含一个函数或类的变量
 - 一个对象上的属性访问
 - 一个函数调用

如果不希望创建出来的实例继承自Object.prototype，你甚至可以在extends后使用null来进行声明。

## Super属性

现在我们学会怎样创建子类了，子类可以继承父类的属性，有时我们会在子类中重新定义同名方法，这样会覆盖掉我们继承的方法。但在某些情况下，如果你重新定义了一个方法，但有时你又想绕开这个方法去使用父类中的同名方法，应该怎样做呢？

假设我们想基于Circle类生成一个子类，这个子类可以通过一些因子来控制圆的缩放，为了实现这一目标，我们写下的这个类看起来有些不太自然：
```javascript
    class ScalableCircle extends Circle {
        get radius() {
            return this.scalingFactor * super.radius;
        }
        set radius() {
            throw new Error("可伸缩圆的半径radius是常量。" +
                            "请设置伸缩因子scalingFactor的值。");
        }
        // 处理scalingFactor的代码
    }
```
请注意radius的getter使用的是super.radius。这里的super是一个全新的关键字，它可以帮我们绕开我们在子类中定义的属性，直接从子类的原型开始查找属性，从而绕过我们覆盖到父类上的同名方法。

通过方法定义语法定义的函数，其原始对象方法的定义在初始化后就已完成，从而我们可以访问它的super属性（也可以访问super[expr]），由于该访问依赖的是原始对象，所以即使我们将方法存到本地变量，再访问时也不会改变super的行为。
```javascript
    var obj = {
        toString() {
            return "MyObject: " + super.toString();
        }
    }
    obj.toString(); // MyObject: [object Object]
    var a = obj.toString;
    a(); // MyObject: [object Object]
```
## 子类化内建方法

你想做的另一件事可能是扩展JavaScript的内建方法。现在你拥有极为强大的JS内建数据结构，它是子类化设计的基础之一，可被用来创建新的类型。假设你想编写一个带版本号的数组，你可以改变数组内容然后提交，或者将数组回滚到之前的状态。我们可以通过子类化Array来快速实现这一功能。
```javascript
    class VersionedArray extends Array {
        constructor() {
            super();
            this.history = [[]];
        }
        commit() {
            // 将变更保存到history。
            this.history.push(this.slice());
        }
        revert() {
            this.splice(0, this.length, this.history[this.history.length - 1]);
        }
    }
```
VersionedArray的实例保留了一些重要的属性，包括map、filter还有sort，它们是组成数组的核心方法；当然，Array.isArray()也会把VersionedArray视为数组；当你向VersionedArray中添加元素时，它的length属性也会自动增长；说远一些，之前能够返回一个新数组的函数（例如Array.prototype.slice()）现在会返回一个VersionedArray！

## 派生类构造函数

你可能已经注意到在上个示例中constructor方法中我们调用了super()方法。调用过程都做了哪些事情呢？

在传统的类模型中，构造函数的作用是为类的实例初始化所有内部状态。每个子类中的构造函数都负责初始化各自的状态。我们希望将这些调用传递下去，以使所有子类都与父类共享相同的初始化代码。

我们可以通过super关键词调用父类中的构造函数，此时它本身也好像是函数一般。请注意，这个语法只在使用extends扩展的子类的constructor方法中有效，通过关键词super可以重写我们的Shape类。
```javascript
    class Shape {
        constructor(color) {
            this._color = color;
        }
    }
    class Circle extends Shape {
        constructor(color, radius) {
            super(color);
            this.radius = radius;
        }
        // 与上文中代码相同
    }
```
在JavaScript中，我们倾向于在构造函数中操作this对象，装载属性并且初始化内部状态。通常情况下，当我们通过new调用构造函数时this对象即被创建，就好像用Object.create()通过构造函数的prototype属性构造对象时所做的那样。然而，有些内建方法的内部对象布局不太一样。例如数组在内存中的布局方式就与普通对象不同。我们想要子类化内建方法，所以我们让最基础的构造函数分配this对象。如果它是一个内建方法，我们会得到我们想要的对象布局；如果它是一个普通构造函数，我们会得到期待中的默认的this对象。

你可能对this在子类构造函数中的绑定方式感到不解。当我们执行基类的构造函数前，this对象没有被分配，从而我们无法得到一个确定的this值。因此，在子类的构造函数中，调用super构造函数之前访问this会触发一个引用错误（ReferenceError）。

正如我们在上一篇文章中看到的，派生类构造函数与constructor方法的省略规则相同，看起来好像你这样写就像这样：
```javascript
    constructor(...args) {
        super(...args);
    }
```
构造函数有时不与this对象交互，它们通过其它方式创建对象并初始化后直接返回，在这种情况下，就不需要再使用super了。此时即使不调用super构造函数，任何构造函数也都能直接返回一个对象。

## new.target

还有一个奇怪的副作用，如果让最基础的类分配this对象，它有时并不知道应该分配哪一种对象。假设你正在写一个对象框架库，你想要一个基类Collection，有一些子类是数组，有一些子类是map。此时，当你准备执行Collection的构造函数时，你将无法区分应该生成哪个类型的对象。

既然我们已经能够子类化内建方法，当我们执行内建构造函数时，在内部我们其实需要知道原始类的prototype属性指向何方。如果没有这个属性，我们无法创建有适当的实例方法的对象。为了解决这个问题，我们加入了一种语法来向JavaScript代码暴露这些信息。我们新添加的元属性new.target与构造函数有关，因为直接通过new来调用，所以它是一个被调函数，在这个函数中调用super会传递new.target的值。

我知道这很难理解，所以我将通过一段代码详细讲解：
```javascript
    class foo {
        constructor() {
            return new.target;
        }
    }
    class bar extends foo {
        // 为了清晰起见我们显式地调用super。
        // 事实上不必去获取这些结果。
        constructor() {
            super();
        }
    }
    // 直接调用foo，所以new.target是foo
    new foo(); // foo
    // 1) 直接调用bar，所以new.target就是bar
    // 2) bar通过super()调用foo，所以new.target仍然是bar
    new bar(); // bar
```
现在，我们已经解决了上述的Collection问题，Collection构造函数可以通过检查new.target来确定子类的类型以及使用哪个内建方法这些不确定的因素。

new.target在任何函数中都是合法的，如果函数不是通过new调用，new.target将被赋值为undefined。

## 鱼和熊掌可以得兼

希望你能坚持读完整篇文章，充实大脑中的知识，我将感激不尽。现在让我们花一点时间来探讨一下，上面这些方法能很好地解决问题么？许多人已经针对“在语言中加入继承的特性是好是坏”这个问题各抒己见，你可能认为对于创建对象来说，继承永远比不上构造（[Composition](https://en.wikipedia.org/wiki/Composition_over_inheritance)） 的方法，换句话说，在与那么多古老的原型模型进行比较后你会发现，代码变得更加简洁需要付出的代价是设计灵活性的缺失。不可否认，在面向对象的世界里，如 果你想创建一些对象，并且它们以扩展的方式共享代码，最好的选择是混入类（Mixins），原因很简单：它能为你提供一种将多个对象的不同方法混入到同一 个对象中的方法，这个方法很简单，因此你无须理解两个不相关的代码片段是如何在相同的继承结构中相互适应的。

很 多人在这个话题上坚决捍卫自己的信念，但是我认为有些事情不值得一昧地坚持。首先，向语言中添加类特性并不意味着要强制使用这些特性，这一态度非常重要； 第二，同样重要的是，向语言中添加类特性也不意味着这就是解决继承问题的最好方式！事实上，有一些问题更适合通过原型继承的方式来建模。在考虑过所有情况 之后，类它只是你可以使用的一个额外的工具而已，它既不是唯一的工具也不一定是最好的工具。

如 果你想继续使用混入类，你可能希望你能有这样一种类，它继承自几个不同的主体，所以你可以继承每一个混入（Mixin）然后获取它们的精华。不幸的是，如 果改变现有的继承模型可能会使整个系统非常混乱，所以JavaScript没有实现类的多继承。那也就是说，在一个基于类的框架内部有一种混合解决方案可 以支持混入类特性。请看下面的这段代码，这是一个基于我们熟知的extend混入习语打造的函数：
```javascript
  function mix(...mixins) {
        class Mix {}
        // 以编程方式给Mix类添加
        // mixins的所有方法和访问器
        for (let mixin of mixins) {
            copyProperties(Mix, mixin);
            copyProperties(Mix.prototype, mixin.prototype);
        }
        return Mix;
    }
    function copyProperties(target, source) {
        for (let key of Reflect.ownKeys(source)) {
            if (key !== "constructor" && key !== "prototype" && key !== "name") {
                let desc = Object.getOwnPropertyDescriptor(source, key);
                Object.defineProperty(target, key, desc);
            }
        }
    }
```

现在，我们无须在各种各样的混入之间创建显示的继承关系，我们只须使用mix函数就可以创建一个组合而成的超类。设想一下，如果你想编写一个协作编辑工具，在这个工具中的所有编辑动作都被记录下来，然后将内容序列化。你可以使用mix函数写一个DistributedEdit类：
```javascript
    class DistributedEdit extends mix(Loggable, Serializable) {
        // 事件方法
    }
```
这真是两全其美啊。如果你想将构造本身有超类的混入类，也可以用这个模型来解决：我们将超类传递给mix函数后它就会返回扩展后的类。

## 目前的可用性
好的，我们已经讨论了许多有关子类化内建方法还有所有的这些新玩意儿，但是你现在可以用这些新技术了么？

事实上，你只能使用其中一部分。在主流浏览器厂商中，Chrome已经移植了我们今天所讨论的大多数特性，在严格模式下，你应该可以实现我们讨论的几乎所有的功能，当然，这不包括子类化Array。其它内建类型也会正常运转，但是数组会有一些不一样；我正在实现Firefox中的相关功能，希望能在不就之后达到与Chrome一样的目标（除了数组之外的每一个功能）。你可以访问[Bug 1141863](https://bugzilla.mozilla.org/show_bug.cgi?id=1141863)获取更多信息，但可能要在数周后才会登陆Nightly版本的Firefox（译者注：截至翻译本文，该功能[已经实现](https://bugzilla.mozilla.org/show_activity.cgi?id=1141863)，将发布于Firefox 44）。

此外，Edge已经支持了super功能，但是没支持子类化内建方法；Safari不支持所有的这些功能。

转译器相对来说处于劣势，它们可以创建类，支持super特性，但是子类化内建方法必须得到引擎的支持，它们需要通过引擎才能从内建方法中得到基类的实例（想想Array.prototype.splice）。
