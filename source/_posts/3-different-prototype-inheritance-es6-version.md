---
title: 3 种不同的原型继承： ES6+ 版本
date: 2016-06-11 20:07:00
updated: 2016-06-11 20:43:25
tags: 
- javascript
- sharejs
- https
categories: 
- js

---
> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[十年踪迹](http://www.zcfy.cc/@akira)
> 链接：[http://www.zcfy.cc/article/425](http://www.zcfy.cc/article/425)
> 原文：[https://medium.com/javascript-scene/3-different-kinds-of-prototypal-inheritance-es6-edition-32d777fa16c9#.q4929515z](https://medium.com/javascript-scene/3-different-kinds-of-prototypal-inheritance-es6-edition-32d777fa16c9#.q4929515z)

这篇文章改编自 [《Programming JavaScript Applications》](https://ericelliottjs.com/product/programming-javascript-applications-paper-ebook-bundle/) 相关章节，我在这基础上扩充了一些内容，并新增了 ES6 部分。

为了更好地发挥 JavaScript 的能力，很重要的一点是理解 JavaScript 的 原生继承能力。这是一个在 JavaScript 实践和学习中经常被忽视的领域，但是理解了它能够获得极大的能力。


<!--more-->


JavaScript 是表现力极强的编程语言之一。其中一个最好的特性是能够在没有类和类继承的情况下创建和继承对象。

结合代理原型(delegate prototypes)、运行时对象扩展和闭包，能够让你用三种不同的原型表达方式实现继承，它们与类继承方式相比有着显著的优点。

要想了解关于类继承的更多弊端，可以观看视频 [“类继承已经过时了：怎样用基于原型的面向对象思维思考”](https://medium.com/media/f0ef8b3751e55cc96193f47ce6958096?maxWidth=1015)：（要翻墙——译者注）

### 代理 / 不同的继承方式

代理原型是一个对象，作为另外一个对象的基类。当你继承一个代理原型，新对象得到一个代理原型对象的引用。

当你要访问新对象的一个属性时，它先检查当前对象自己的属性，如果没有找到，它检查 `[[Prototype]]`，然后继续沿着**原型链**往上查找，直到它到达 `Object.prototype`，`Object.prototype` 是大多数对象的根代理（root delegate）。

![ecmascript6_prototype.png][1]

方法代理（method delegation）可以节省内存资源，因为你只需要为每个方法准备一份拷贝，它通过原型被所有的实例共享。有许多办法可以做到这一点，在 ES6 中可能看起来像下面这样：

```js
class Greeter {
  constructor (name) {
    this.name = name || 'John Doe';
  }
  hello () {
    return `Hello, my name is ${ this.name }`;
  }
}

const george = new Greeter('George');

const msg = george.hello();

console.log(msg); // Hello, my name is George
```

由于传统继承和类的扩展有许多问题，我不推荐使用这个技术。我将它展示在这里只是因为它与传统面向对象的模式类似，所以大家对这种方式可能比较熟悉。

你可能对用 ES5 构造函数的方式也比较熟悉：

```js
function Greeter (name) {
  this.name = name || 'John Doe';
}

Greeter.prototype.hello = function hello () {
  return 'Hello, my name is ' + this.name;
}

var george = new Greeter('George');

var msg = george.hello();

console.log(msg); // Hello, my name is George
```

我更喜欢使用 `Object.create()` 实现**工厂函数（factory function）**的方式。（在 JavaScript 中，任何函数都可以用来创建对象。当它不是一个构造函数时，它被叫做工厂函数）：

```js
const proto = {
  hello () {
    return `Hello, my name is ${ this.name }`;
  }
};

const greeter = (name) => Object.assign(Object.create(proto), {
  name
});

const george = greeter('george');

const msg = george.hello();

console.log(msg);
```

如果你不需要代理属性，你可以用 `Object.create(null)` 把 prototype 设置成 `null`。

使用原型来代理的主要缺点是它不好存储状态。如果你要用对象或者存储来存储状态，改变数组或对象的任何一个成员会改变同时每一个实例上的内容，因为它保存在原型上。为了解决这个问题，你需要将它拷贝到每个对象上。

### 拼接继承 / 克隆 / 混合

拼接继承是一个从一个对象向另一个对象拷贝属性的过程，它不保持两个对象引用同一个对象。它依赖于 JavaScript 的动态对象扩展特性。

克隆一个非常好的保存对象默认状态的方式：ES6 中它可以通过 `Obejct.assign()` 实现，这个方法和 Lodash、Underscore 以及 jQuery 的 `extend()` 方法类似。

```js
const proto = {
  hello: function hello() {
    return `Hello, my name is ${ this.name }`;
  }
};

const george = Object.assign({}, proto, {name: 'George'});

const msg = george.hello();

console.log(msg); // Hello, my name is George
```

这个形式在混合模式（mixins)中也很常见。例如，你可以通过混合 `EventEmitter3` 的原型将任意一个对象变成事件派发器：

```js
import Events from 'eventemitter3';

const object = {};

Object.assign(object, Events.prototype);

object.on('event', payload => console.log(payload));

object.emit('event', 'some data'); // 'some data'
```

我们可以使用它来创建一个 Backbone 风格的事件派发模型：

```js
import Events from 'eventemitter3';

const modelMixin = Object.assign({
  attrs: {},
  set (name, value) {
    this.attrs[name] = value;

    this.emit('change', {
      prop: name,
      value: value
    });
  },

  get (name) {
    return this.attrs[name];
  }
}, Events.prototype);


const george = { name: 'george' };
const model = Object.assign(george, modelMixin);

model.on('change', data => console.log(data));

model.set('name', 'Sam');
/*
{
  prop: 'name',
  value: 'Sam'
}
*/
```

拼接继承非常强大，但你还可以结合闭包将它变得更强大。

### 函数式继承

_别将它和函数式编程混为一谈。_

在 《JavaScript: The Good Parts》 中，Douglas Crockford 创造了函数式继承。函数式继承使用一个工厂函数，然后使用拼接继承将新属性添加进来。

如果一个函数创建的目的是扩展已有对象，这个模式通常被称为_函数式混合_。使用函数来扩展的主要好处是它能让你使用函数闭包来封装私有数据。换句话说，你可以执行私有状态。

将私有属性挂载在公有属性上有点小尴尬，用户不用通过调用合适的函数就能读写它们。我们确实想要将私有属性通过闭包（可以进一步了解[“什么是闭包”](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)）对用户隐藏起来。看下面这个例子：

```js
import Events from 'eventemitter3';

const rawMixin = function () {
  const attrs = {};

  return Object.assign(this, {
    set (name, value) {
      attrs[name] = value;

      this.emit('change', {
        prop: name,
        value: value
      });
    },

    get (name) {
      return attrs[name];
    }
  }, Events.prototype);
};

const mixinModel = (target) => rawMixin.call(target);

const george = { name: 'george' };
const model = mixinModel(george);

model.on('change', data => console.log(data));

model.set('name', 'Sam');
/*
{
  prop: 'name',
  value: 'Sam'
}
*/
```

通过将 `attrs` 从一个公有属性变成一个私有变量，我们从公共 API 上移除了所有 attrs 相关的内容。唯一可以使用它的方式只能是通过**特权方法**，即那些定义在闭包的函数作用域中的方法，这些方法可以访问到私有数据。

注意上面的例子，我们使用 `mixinModule()` 包装实际的函数式混合 `rawMixin()`。这么做的原因是我们需要将 `this` 设置到函数中，我们通过 `Function.prototype.call()` 来做到这一点。我们可以不用这个 wrapper，将 `this` 通过参数直接传进去，但我不喜欢那样做。

### 组合模式好于类继承

> _“对象组合好于类继承” ~ 四人组, [《Design Patterns: Elements of Reusable Object Oriented Software》](http://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/)（《Design Patterns: Elements of Reusable Object-Oriented Software》，由 Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides 合著（Addison-Wesley，1995）。这几位作者常被称为“四人组（Gang of Four）”。——译者注）_

传统继承通过限制性分类创建了“归属（is-a）”关系，在新型用例里面，这种描述关系通常最终不适用。事实证明，我们经常采用“有一个（has-a）”、“使用一个（uses-a）” 或者 “能做（can-do）”关系。

组合模式更像一个全能的吉他手。无论想要什么样的效果都没问题，只需要将对应的功能插入进来。

```
const effect = compose(delay, distortion, robovoice); // Rock on!
```

> 什么时候你想要使用类继承？对于我来说，答案很简单：“从不使用。”

#### 组合模式：

*   **更简单**
*   **表现力更强**
*   **更灵活**

[观看视频 “Composition Over Inheritance” 了解更多信息](https://medium.com/media/073540706bc03f8ed7045546154c410a?maxWidth=700)。（也要翻墙——译者注）

### Stamps

Stamps 是**可组合的函数工厂（composable factory functions）**。我创建了 stamps 作为一个实验性的示例用在我的书 [《Programming JavaScript Applications》](http://pjabook.com/) 中。一切开始于以下有趣的挑战：

> 一种模拟 JavaScript 类的语法糖（我强烈不鼓励使用 JavaScript 类）。如果我们为 JavaScript 基于原型的面向对象创建类语法糖，并支持所有好的特性，那么它看起来是怎样的？

探索的结果得到了目前据我所知最受欢迎的 JavaScript 原型继承库：[Stampit](https://github.com/stampit-org/stampit)。

stampit 的设计被很多人认同，没过多久，它的衍生库开始出现。特别是，Tim Routowicz 写了杰出的 [react-stamp](https://github.com/stampit-org/react-stamp) 库用于可组合的 React 组件。我们需要一个标准让我们的 API 实现能够彼此一致。

于是我们有了 [Stamp 规范](https://github.com/stampit-org/stamp-specification)。

一个 **stamp** 是一个可组合的工厂函数，它基于它的**描述符（descriptor）**返回对象实例。

```js
stamp(options?: Object, ...args?: [...Any]) => instance: Object
```

每个 stamp 有一个方法叫做 `compose()`：

```js
Stamp.compose(...args?: [...Composable]) => Stamp
```

当 `compose()` 方法被调用，它以当前 stamp 为基础构造一个新的 stamp，一组**可被组合**元素作为参数被传入作为被组合元素列表被组合起来： 

```js
const combinedStamp = baseStamp.compose(composable1, composable2, composable3);
```

一个**可被组合**元素是一个 stamp 或者一个 POJO（Plain Old JavaScript Object） stamp 描述符。

`compose()` 还可以代表 stamp 的**描述符**。换句话说，描述符的，属性被附加到 stamp 的 `.compose()` 方法上，比如 `stamp.compose.methods`。

#### 描述符

**可组合描述符**（或者简称**描述符**）是一个元素数对象，它包含创建一个对象实例的必要信息。

**一个描述符包含：**

* **methods** - 将被添加到对象原型上的方法集。
* **properties** - 将被添加到对象原型上的属性集。
* **initializers** - 一个函数数组，它会按顺序执行。Stamp 的细节和参数会被依次传给这些函数。
* **staticProperties** - 将被拷贝和添加到 stamp 本身之上的静态属性集。

描述符还有其他一些属性，但是它们不是最常用的关键属性。你可以查看 [descriptor 规范](https://github.com/stampit-org/stamp-specification#the-stamp-descriptor)来进一步了解全貌。

### 为什么用 stamp？

原型继承很棒，JavaScript 的能力给了我们非常强大的工具去探索它的更多用法，但是它可以被约束得更加方便实用。

一些基本问题例如“我怎样通过一个授权方法访问私有数据？”以及“实现多重继承有哪些好的选择？”是许多 JavaScript 使用者都遇到过的难题。

让我们使用 `stamp-utils` 库中的 `init()` 和 `compose()` 来回答所有这些问题。

* **`compose(...composables:[...Composable]) => Stamp`** 接收任意个 composeables 参数并返回一个新的 stamp。
* **`init(...functions:[...Function]) => Stamp` 接收任意个 initializer 函数并返回一个新的 stamp。

首先，我们会使用一个闭包来创建数据的私有空间：

```js
const a = init(function () {
  const a = 'a';

  Object.assign(this, {
    getA () {
      return a;
    }
  });
});

console.log(typeof a()); // 'object'
console.log(a().getA()); // 'a'
```

它使用函数作用域来封装私有数据。注意 getter 必须定义在函数中，以便于访问到闭包内的变量。

这是另一个例子：

```js
const b = init(function () {
  const a = 'b';

  Object.assign(this, {
    getB () {
      return a;
    }
  });
});
```

getB 中返回 `a` 不是我写错了，我故意这样写来说明两个 init 的私有变量叫同样的名也可以，getter 名字不一样就行，compose 的时候彼此不冲突：

```js
const c = compose(a, b);

const foo = c();
console.log(foo.getA()); // 'a'
console.log(foo.getB()); // 'b'
```

很好玩吧？上面同时继承了两个从各自私有域访问私有属性的授权方法。

但它简单得有点无聊，让我们看看还有什么玩法：

```js
import { compose, init } from 'stamp-utils';

// 更多授权方法，操作一些私有数据。
const availability = init(function () {
  let isOpen = false; // private

  Object.assign(this, {
    open () {
      isOpen = true;
      return this;
    },
    close () {
      isOpen = false;
      return this;
    },
    isOpen () {
      return isOpen;
    }
  });
});

//这是一个 stamp，它包含一些公有方法，和一些状态：
const membership = compose({
  methods: {
    add (member) {
      this.members[member.name] = member;
      return this;
    },
    getMember (name) {
      return this.members[name];
    }
  },
  properties: {
    members: {}
  }
});

// 让我们设置一些默认值：
const defaults = compose({
  properties: {
    name: 'The Saloon',
    specials: 'Whisky, Gin, Tequila'
  }
});

const overrides = init(function (overrides) {
  Object.assign(this, overrides);
});

// 传统继承实现不了下面这个模式。这个模式没有父类/子类耦合。
// 不用一层一层深深嵌套来做多重继承
// 代码良好、干净和可重用
const bar = compose(availability, membership, defaults, overrides);
const myBar = bar({name: 'Moe\'s'});

// 看起来有点笨，但保证了一切是一切该有的样子。
const result = myBar.add({name: 'Homer'}).open().getMember('Homer');
console.log(result); // { name: 'Homer' }
console.log(`
  name: ${ myBar.name }
  isOpen: ${ myBar.isOpen() }
  specials: ${ myBar.specials }
`);
/*
  name: Moe's
  isOpen: true
  specials: Whisky, Gin, Tequila
*/
```

### 那 `class` 怎么样？

如你所见，JavaScript 提供了一个非常有弹性的对象系统，它不需要依赖 `class`。那么为什么我们要添加 `class` 到我们的首选项里？因为许多人对其他语言中的 `class` 范式更熟悉，所以人们总是不断尝试在 JavaScript 中去模拟它。

> 继承在 JavaScript 中实现起来如此容易，容易到让那些想要实现它的人感到迷惑。为了让它变得难一些，我们添加了 `class`。

好几个著名的 JavaScript 框架实现了模仿传统继承的机制，它们通过原型链来模拟类继承。添加一个官方的 `class` 关键字提供了一个规范的方法去实现类继承，但是依我的观点，[你应该避免使用它](https://medium.com/javascript-scene/the-two-pillars-of-javascript-ee6f3281e7f3)。在 JavaScript 中，组合模式比类继承更简单，表现力更强，而且更加灵活。我没想到在什么使用场景下 `class` 能比原生的原型模式表现得更好。

几年来，我向人们发起挑战，看谁能提供一个使用场景，说明在这个场景下必须用类继承，然而目前为止还没看到有什么场景用类继承好，相反，我听到一大堆 [常见误解](https://medium.com/javascript-scene/5-common-misconceptions-about-tdd-unit-tests-863d5beb3ce9)。

### 结论

一旦你开始考虑不用 class 创建对象，继承采用原型，通过级联和 stamp 组合对象，你将会惊叹于 JavaScript 对象系统竟能变得如此简单、功能强大和灵活。

最近 stamp 社区成长了许多。我们已经创建了很多基于 stamp 的库和工具类。最被广泛使用的包括 [Stampit](https://github.com/stampit-org/stampit) (Stamp 的原始库)、 [react-stamp](https://github.com/stampit-org/react-stamp) 以及 [stamp-utils](https://github.com/stampit-org/stamp-utils)。

这篇文章的所有例子都使用 `stamp utils` 编写。它提供了一个 `compose()` 方法的最小实现，附带一个 `init()` 函数，它是 compose 的一个语法糖：

```js
const init = (...functions) => compose({ initializers: [...functions] });
```

下一次你需要创建有状态的对象产生许多实例，可以尝试使用 stamps。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/06/874139798.png