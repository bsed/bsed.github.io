---
title: "ECMAScript6教程 模板字符串 template strings"
categories: [ "JS" ]
tags: [ "template","ecmascript6" ]
draft: false
slug: "ecmascript6-tutorial-template-string-strings-template"
date: "2015-11-28 19:57:00"
---

> [https://hacks.mozilla.org/2015/05/es6-in-depth-template-strings-2/](https://hacks.mozilla.org/2015/05/es6-in-depth-template-strings-2/)

> 作者：[Jason Orendorff](https://blog.mozilla.org/jorendorff/)
> 翻译: 刘振涛

## 反撇号（`）基础知识

ES6引入了一种新型的字符串字面量语法，我们称之为模板字符串（template strings）。除了使用反撇号字符 ` 代替普通字符串的引号 ' 或 " 外，它们看起来与普通字符串并无二致。在最简单的情况下，它们与普通字符串的表现一致：

    context.fillText(`Ceci n'est pas une chaîne.`, x, y);

但是我们并没有说：“原来只是被反撇号括起来的普通字符串啊”。模板字符串名之有理，它为JavaScript提供了简单的[字符串插值功能](https://en.wikipedia.org/wiki/String_interpolation)，从此以后，你可以通过一种更加美观、更加方便的方式向字符串中插值了。

模板字符串的使用方式成千上万，但是最让我会心一暖的是将其应用于毫不起眼的错误消息提示：


<!--more-->


```javascript

    function authorize(user, action) {
      if (!user.hasPrivilege(action)) {
        throw new Error(
          `User ${user.name} is not authorized to do ${action}.`);
      }
    }
```

在这个示例中，`${user.name}`和`${action}`被称为模板占位符，JavaScript将把`user.name`和`action`的值插入到最终生成的字符串中，例如： `User jorendorff is not authorized to do hockey.`。（这是真的，我还没有获得冰球许可证。）

到目前为止，我们所了解到的仅仅是比 `+` 运算符更优雅的语法，下面是你可能期待的一些特性细节：

- 模板占位符中的代码可以是任意JavaScript表达式，所以函数调用、算数运算等这些都可以作为占位符使用，你甚至可以在**一个模板字符串中嵌套另一个**，我称之为模板套构（template inception）。

- 如果这两个值都不是字符串，可以按照常规将其转换为字符串。*例如*：如果`action`是一个对象，将会调用它的`.toString()`方法将其转换为字符串值。

- 如果你需要在模板字符串中书写反撇号，你必须使用反斜杠将其转义：`\`` 等价于 "`" 。

- 同样地，如果你需要在模板字符串中引入字符`${`。无论你要实现什么样的目标，你都需要用反斜杠转义每一个字符：``write \${ or $\{``
与普通字符串不同的是，模板字符串可以多行书写：

```javascript

	$("#warning").html(`
            <h1>Watch out!</h1>
            <p>Unauthorized hockeying can result in penalties
            of up to ${maxPenalty} minutes.</p>
         `);
```
模板字符串中所有的空格、新行、缩进，都会原样输出在生成的字符串中。

好啦，我说过要让你们轻松掌握模板字符串，从现在起难度会加大，你可以到此为止，去喝一杯咖啡，慢慢消化之前的知识。真的，及时回头不是一件令人感到羞愧的事情。[Lopes Gonçalves](https://en.wikipedia.org/wiki/Lopes_Gon%C3%A7alves) 曾经向我们证明过，船只不会被海妖碾压，也不会从地球的边缘坠落下去，他最终跨越了赤道，但是他有继续探索整个南半球么？并没有，他回家了，吃了一顿丰盛的午餐，你一定不排斥这样的感觉。

## 反撇号的未来

当然，模板字符串也并非事事包揽：

- 它们不会为你自动转义特殊字符，为了避免[跨站脚本](http://www.techrepublic.com/blog/it-security/what-is-cross-site-scripting/)漏洞，你应当像拼接普通字符串时做的那样对非置信数据进行特殊处理。

- 它们无法很好地与[国际化库](http://yuilibrary.com/yui/docs/intl/)（可以帮助你面向不同用户提供不同的语言）相配合，模板字符串不会格式化特定语言的数字和日期，更别提同时使用不同语言的情况了。

- 它们不能替代模板引擎的地位，例如：[Mustache](https://mustache.github.io/)、[Nunjucks](https://mozilla.github.io/nunjucks/)。

模板字符串没有内建循环语法，所以你无法通过遍历数组来构建类似HTML中的表格，甚至它连条件语句都不支持。你当然可以使用**模板套构**（template inception）的方法实现，但在我看来这方法略显愚钝啊。

不过，ES6为JS开发者和库设计者提供了一个很好的衍生工具，你可以借助这一特性突破模板字符串的诸多限制，我们称之为**标签模板**（tagged templates）。

标签模板的语法非常简单，在模板字符串开始的反撇号前附加一个额外的标签即可。我们的第一个示例将添加一个`SaferHTML`标签，我们要用这个标签来解决上述的第一个限制：自动转义特殊字符。

请注意，ES6标准库不提供类似`SaferHTML`功能，我们将在下面自己来实现这个功能。
```javascript

	var message =
        SaferHTML`<p>${bonk.sender} has sent you a bonk.</p>`;
```

这里用到的标签是一个标识符`SaferHTML`；也可以使用属性值作为标签，例如：`SaferHTML.escape`；还可以是一个方法调用，例如：`SaferHTML.escape({unicodeControlCharacters: false})`。精确地说，任何ES6的[成员表达式（MemberExpression）或调用表达式（CallExpression）都可作为标签使用](https://people.mozilla.org/~jorendorff/es6-draft.html#sec-left-hand-side-expressions)。

可以看出，无标签模板字符串简化了简单字符串拼接，标签模板则完全简化了函数调用！

上面的代码等效于：
```javascript

    var message =
      SaferHTML(templateData, bonk.sender);
```
templateData是一个不可变数组，存储着模板所有的字符串部分，由JS引擎为我们创建。因为占位符将标签模板分割为两个字符串的部分，所以这个数组内含两个元素，形如[Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)(["<p>", " has sent you a bonk.</p>"]。

（事实上，templateData中还有一个属性，在这篇文章中我们不会用到，但是它是标签模板不可分割的一环：templateData.raw，它同样是一个数组，存储着标签模板中所有的字符串部分，如果我们查看源码将会发现，在这里是使用形如\n的转义序列分行，而在templateData中则为真正的新行，标准标签String.raw会用到这些原生字符串。）

如此一来，SaferHTML函数就可以有成千上万种方法来解析字符串和占位符。

在继续阅读以前，可能你苦苦思索到底用SaferHTML来做什么，然后着手尝试去实现它，归根结底，它只是一个函数，你可以在Firefox的开发者控制台里测试你的成果。

以下是一种可行的方案（在[gist](https://gist.github.com/jorendorff/1a17f69dbfaafa2304f0)中查看）：

```javascript

	function SaferHTML(templateData) {
	  var s = templateData[0];
	  for (var i = 1; i < arguments.length; i++) {
	    var arg = String(arguments[i]);

	    // 转义占位符中的特殊字符。
	    s += arg.replace(/&/g, "&")
	            .replace(/</g, "<")
	            .replace(/>/g, ">");

	    // 不转义模板中的特殊字符。
	    s += templateData[i];
	  }
	  return s;
	}
```

通过这样的定义，标签模板SaferHTML`<p>${bonk.sender} 向你示好。</p>` 可能扩展为字符串 "<p>ES6<3er 向你示好。</p>"。即使一个恶意命名的用户，例如“`黑客Steve<script>alert('xss');</script>`”，向其他用户发送一条骚扰信息，无论如何这条信息都会被转义为普通字符串，其他用户不会受到潜在攻击的威胁。

（顺便一提，如果你感觉上述代码中在函数内部使用[参数对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)的方式令你感到枯燥乏味，不妨期待下一篇大作，ES6中的另一个新特性一定会让你眼前一亮！）

仅一个简单的示例不足以说明标签模板的灵活性，我们一起回顾下我们之前有关模板字符串限制的列表，看一下你还能做些什么不一样的事情。

> 模板字符串不会自动转义特殊字符。但是正如我们看到的那样，通过标签模板，你可以自己写一个标签函数来解决这个问题。
事实上，你可以做的比那更好。

站在安全角度来说，我实现的SaferHTML函数相当脆弱，你需要通过多种不同的方式将HTML不同部分的特殊字符转义，SaferHTML就无法做到全部转义。但是稍加努力，你就可以写出一个更加智能的SaferHTML函数，它可以针对templateData中字符串中的HTML位进行解析，分析出哪一个占位符是纯HTML；哪一个是元素内部属性，需要转义'和"；哪一个是URL的query字符串，需要进行URL转义而非HTML转义，等等。智能SaferHTML函数可以将每个占位符都正确转义。

HTML的解析速度很慢，这种方法听起来是否略显牵强？幸运的是，当模板重新求值的时候标签模板的字符串部分是不改变的。SaferHTML可以缓存所有的解析结果，来加速后续的调用。（缓存可以按照ES6的另一个特性——**WeakMap**的形式进行存储，我们将在未来的文章中继续深入讨论。）

> 模板字符串没有内建的国际化特性，但是通过标签，我们可以添加这些功能。Jack Hsu的一篇博客文章展示了具体的实现过程。我谨在此处抛砖引玉：
```javascript

	i18n`Hello ${name}, you have ${amount}:c(CAD) in your bank account.`
	// => Hallo Bob, Sie haben 1.234,56 $CA auf Ihrem Bankkonto.
```

注意观察这个示例中的运行细节，name和amount都是JavaScript，进行正常插值处理，但是有一段与众不同的代码，:c(CAD)，Jack将它放入了模板的字符串部分。JavaScript理应由JavaScript引擎进行处理，字符串部分由Jack的i18n标签进行处理。使用者可以通过i18n的文档了解到，:c(CAD)代表加拿大元的货币单位。

这就是标签模板的大部分实际应用了。

- 模板字符串不能代替Mustache和Nunjucks，一部分原因是在模板字符串没有内建的循环或条件语句语法。我们一起来看如何解决这个问题，如果JS不提供这个特性，我们就写一个标签来提供相应支持。

```javascript

	// 基于纯粹虚构的模板语言
	// ES6标签模板。
	var libraryHtml = hashTemplate`
	  <ul>
	    #for book in ${myBooks}
	      <li><i>#{book.title}</i> by #{book.author}</li>
	    #end
	  </ul>
	`;
```
标签模板带来的灵活性远不止于此，要记住，标签函数的参数不会自动转换为字符串，它们如返回值一样，可以是任何值，标签模板甚至不一定要是字符串！你可以用自定义的标签来创建正则表达式、DOM树、图片、以promises为代表的整个异步过程、JS数据结构、GL着色器……

标签模板以开放的姿态欢迎库设计者们来创建强有力的领域特定语言。这些语言可能看起来不像JS，但是它们仍可以无缝嵌入到JS中并与JS的其它语言特性智能交互。我不知道这一特性将会带领我们走向何方，但它蕴藏着无限的可能性，这令我感到异常兴奋！

## 我什么时候可以开始使用这一特性？
在服务器端，io.js支持ES6的模板字符串。

在浏览器端，Firefox 34+支持模板字符串。它们由去年夏天的实习生项目组里的Guptha Rajagopal实现。模板字符串同样在Chrome 41+中得以支持，但是IE和Safari都不支持。到目前为止，如果你想要在web端使用模板字符串的功能，你将需要[Babel](http://babeljs.io/)或[Traceur](https://github.com/google/traceur-compiler#what-is-traceur)协助你完成ES6到ES5的代码转译，你也可以在[TypeScript](http://blogs.msdn.com/b/typescript/archive/2015/01/16/announcing-typescript-1-4.aspx)中立即使用这一特性。

## 等等——那么Markdown呢？
嗯？

哦…这是个好问题。

（这一章节与JavaScript无关，如果你不使用 [Markdown]http://daringfireball.net/projects/markdown/basics()，可以跳过这一章。）

对于模板字符串而言，Markdown和JavaScript现在都使用`字符来表示一些特殊的事物。事实上，在Markdown中，反撇号用来分割在内联文本中间的代码片段。

这会带来许多问题！如果你在Markdown中写这样的文档：

	To display a message, write `alert(`hello world!`)`.

它将这样显示：

	To display a message, write alert(hello world!).

请注意，输出文本中的反撇号消失了。Markdown将所有的四个反撇号解释为代码分隔符并用HTML标签将其替换掉。

为了避免这样的情况发生，我们要借助Markdown中的一个鲜为人知的特性，你可以使用多行反撇号作为代码分隔符，就像这样：

	To display a message, write ``alert(`hello world!`)``.

在这个[Gist](https://gist.github.com/jorendorff/d3df45120ef8e4a342e5)有具体代码细节，它由Markdown写成，所以你可以直接查看源代码。