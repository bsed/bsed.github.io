---
title: "ECMAScript6教程 模块 Modules　(二)"
categories: [ "JS" ]
tags: [ "ecmascript6","modules" ]
draft: false
slug: "ecmascript6-tutorial-module-modules-two"
date: "2015-12-28 17:55:00"
---

## 聚合模块

有时一个程序包中主模块的代码比较多，为了简化这样的代码，可以用一种统一的方式将其它模块中的内容聚合在一起导出，可以通过这种简单的方式将所有所需内容导入再导出：

```javascript
    // world-foods.js - 来自世界各地的好东西

    // 导入"sri-lanka"并将它导出的内容的一部分重新导出
    export {Tea, Cinnamon} from "sri-lanka";

    // 导入"equatorial-guinea"并将它导出的内容的一部分重新导出
    export {Coffee, Cocoa} from "equatorial-guinea";

    // 导入"singapore"并将它导出的内容全部导出
    export * from "singapore";
```
这些export-from语句每一个都好比是在一条import-from语句后伴随着一个export。与真正的导入内容的方法不同的是，这些导入内容再重新导出的方法不会在作用域中绑定你导入的内容。如果你打算用world-foods.js中的Tea来写一些代码，可别用这种方法导入模块，你会发现当前模块作用域中根本找不到Tea。


<!--more-->


如果从“singapore”导出的任何名称碰巧与其它的导出冲突了，可能会触发一个错误，所以使用export *语句的时候要格外小心。

呼！终于讲完了所有的语法！现在来讲一些有趣的内容。

## import实际都做了些什么？

如果我说它什么都没做，你敢信？

哦，看来你没那么容易上当啊。好吧，你相信标准里面通常都不会规定import的行为么？如果真是这样，那这是件好事儿么？

ES6将模块加载过程的细节完全交由[最终的实现来定义](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-hostresolveimportedmodule)，模块执行的其它部分倒是在[规范中有详细定义](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-toplevelmoduleevaluationjob)。

粗略地讲，当你通知JS引擎运行一个模块时，它一定会按照以下四个步骤执行下去：

 1. 语法解析：阅读模块源代码，检查语法错误。
 2. 加载：递归地加载所有被导入的模块。这也正是没被标准化的部分。
 3. 连接：每遇到一个新加载的模块，为其创建作用域并将模块内声明的所有绑定填充到该作用域中，其中包括由其它模块导入的内容。
 4. 如果你的代码中有import {cake} from "paleo"这样的语句，而此时“paleo”模块并没有导出任何“cake”，你就会触发一个错误。这实在是太糟糕了，你都快要运行模块中的代码了，都是cake惹的祸！
 5. 运行时：最终，在每一个新加载的模块体内执行所有语句。此时，导入的过程就已经结束了，所以当执行到达有一行import声明的代码的时候……什么都没发生！

看到了嘛？我可告诉过你结果是“啥都没有”哦。事关编程语言我绝不撒谎！

但是现在我们真的要深入了解这个系统最有趣的部分了！有一个很酷的小技巧我可以教给你。系统不指定加载过程的实现方式，你也可以通过在源代码中查找import声明提前计算出所有依赖，你可以将ES6系统实现为：在编译时计算所有依赖并将所有模块打包成一个文件，通过网络一次传输所有模块！像[webpack](http://www.2ality.com/2015/04/webpack-es6.html)这样的工具就实现了这个功能。

这种做法的意义非常深远，因为通过网络加载脚本需要花费时间，每当你请求到一个模块，你可能发现它里面也包含着import声 明，这就需要你再花费一些时间加载更多的脚本。基于如此天真的思想实现的加载器需要消耗更多的网络往返时间。但是webpack就不一样啦，它所用的加载 器是经过精心设计的，吸收了软件工程领域的精华，所以你不仅可以立即开始使用ES6模块系统，还不会损耗运行时的性能。

最初的时候，标准委员会已经制定并实现了详细的ES6模块加载标准，它未成为最终的标准的原因是成员们没有就代码封包（bundle）功能的实现方式达成一 致意见。我希望有人能搞定这个问题，正如我们所见，模块加载的过程亟待被标准化；最关键的是，封包的功能实在是太好，就这样放弃对其进行标准化有些可惜 啊。


## 静态vs动态：论规则及破例之法

JavaScript作为一门动态语言已经得到了一个令人惊讶的静态模块系统。

你只可以在模块的最外层作用域使用import和export，不可在条件语句中使用，也不能在函数作用域中使用import。

所有导出的标识符一定要在源代码中明确地导出它们的名称，你不能通过编写代码遍历一个数组然后用数据驱动的方式导出一堆名称。

模块对象被冻结了，所以你无法hack模块对象并为其添加polyfill风格的新特性。

一个模块的所有依赖必须在模块代码运行前完全加载、解析并且及早连接，不存在一种通过import来按需懒加载的语法。

import模块产生的错误没有错误恢复机制。一个app可能囊括了上百个模块，一旦有一个模块无法加载或连接，所有的模块都不会运行，而且你不能在try/catch代码块中捕捉import的错误信息。（上面这些描述的本意是说：系统是静态的，webpack可在编译时为你检测那些错误。）
不支持在模块加载依赖前运行其它代码的钩子，这也意味着无法控制模块的依赖加载过程。

只要你的需求是静态的，系统就会运行良好，但是你有时可以设想下需要一点儿hack，对么？

这也就是无论你用什么模块加载系统，你都将有一个编程API来支持ES6的静态import/export语法。举个例子，webpack中引入了一个“[代码分割”API](http://webpack.github.io/docs/code-splitting.html)，从而可以按需懒加载一些模块的多个封包。相同的API可以帮你打破上面列举的绝大多数其它的规则。

ES6模块语法非常静态，这是很好的——它通过强有力的编译时工具的形式进行弥补。但是设计静态语法的初衷是要与丰富的动态编程加载器API一起增强ES6的模块系统。

## 我什么时候可以使用ES6模块？

如果你现在就想在项目中加入新的模块语法，你需要使用[Babel](http://babeljs.io/)或[Traceur](https://github.com/google/traceur-compiler#what-is-traceur)这样的转译器。在系列之前的文章中，Gastón I. Silva展示了如何使用Babel和Broccoli来为web平台编译ES6代码；在那篇文章的基础上，Gastón准备了一个[支持ES6模块的工作示例](https://github.com/givanse/broccoli-babel-examples/tree/master/es6-modules)。[Axel Rauschmayer写的这篇](http://www.2ality.com/2015/04/webpack-es6.html)文章给出了一个用Babel和webpack构建项目的示例。

ES6模块系统主要由Dave Herman和Sam Tobin-Hochstadt进行设计，在近几年的争论中，他们与所有参与者（包括我）为新模块系统的静态部分进行辩护。Jon Coppeard负责在Firefox中实现这些模块的特性。[JavaScript加载器标准](https://github.com/whatwg/loader)