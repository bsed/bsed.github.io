---
title: "单页应用(SPA)开发中的 Top 10 框架"
categories: [ "JS" ]
tags: [ "javascript","zcfy","spa","framework" ]
draft: false
slug: "single-page-application-spa-development-in-the-framework-of-the-top-10"
date: "2016-06-12 20:45:00"
---

> 本文转载自：[众成翻译](http://www.zcfy.cc)
> 译者：[chbinghu](http://www.zcfy.cc/@chbinghu)
> 链接：[http://www.zcfy.cc/article/272](http://www.zcfy.cc/article/272)
> 原文：[http://noeticforce.com/best-Javascript-frameworks-for-single-page-modern-web-applications](http://noeticforce.com/best-Javascript-frameworks-for-single-page-modern-web-applications)

![javascript_framework_2016.jpg][1]
要用原生 JavaScript 开发 web 应用 ？ 呵呵哒！现在都追求开发效率，开发者需要要更高效的方式，于是 JavaScript 框架诞生了。JavaScript 框架是单页应用开发的顶梁柱，它为 HTML 和 原生 JavaScript 增加了强大的功能。

多年以来，我们苦逼地使用原生 JavaScirpt 和 jQuery 开发复杂的界面，开发和维护的苦真是一言难尽。

框架能够让开发者抽出更多的精力专注在交互功能上，不用太操心代码的结构和组织。

大多数框架使用了 MVC 模式，并增强了代码的扩展性，重用性和可维护性。MVC 并不是唯一的准则，还有一些其他的模式，比如 MV＊， MVVM， MVP。 要根据项目的需求应用恰当的模式。

最近一段时间 JavaScirpt 框架层出不穷，其中有十个最棒的，它们高端大气，功能丰富，广受欢迎，让我们可以搞定那些难搞的单页应用。


<!--more-->


## 1. Angular.js
AngularJS，Google 的亲儿子，是 JavaScript 框架中一面旗帜，在2009年发布第一版，遵循 MIT 许可开源。发布以来，Angular 的生态圈就以惊人的速度壮大。它自称拥有最大的开发者社区，也是最常用的框架。

Angular 为 HTML  增添了开发动态交互页面时所需的全部功能，其中包括在 HTML 元素的属性上添加 Angular 的指令。用 Angular 扩展 HTML 太方便了，把默认指令或者自定义指令放在某个 div 的属性上就可以了。

Angular 负责编译和渲染 HTML，并生成用户界面， 在此过程中，它操作 DOM 并实现指令中的全部功能。不过，指令只是 Angular 强大功能的一部分。

双向数据绑定是 Angular 的核心功能。用户在界面上戳戳点点或是输入点什么的时候，改变了应用中的 view，此时 model(JavaScript objects) 会跟随着 view 一起发生变化，model 中的代码逻辑得到执行，最终将更新 DOM 结构。

这个过程反过来也一样的，当某些因素引起 model 变化时（比如使用服务端的消息推送），view 会重新渲染。这种方式大体上根治了手工维护 DOM 的痛苦。

最近发布的 ReactJS 有超越 Angular 的势头，不过 Angular 牢牢地把持着领先地位，人们对它的需求有增无减。Angular 能够不断地成长是因为
每一个版本都会有优化和先进的功能。

我们可以访问 Angular 的官方网张了解更多的信息-[angularjs.org](https://angularjs.org/)


## 2. React.js
Facebook 和 Instagram 的用户界面为什么这么给力呢？因为它们用的是 React.js。我们借此可以感受到 ReactJS 在创建大规模动态应用方面的强大能力。

React 在 2013 年发布了第一个的开源版本，它遵循 BSD 许可。一经发布社区规模就迅速扩大，必须承认它是当今发展最快 JavaScript 框架。随手就可以找到非常多的资料，教程和 React 的组件库。

ReactJS 最擅长高效地渲染复杂的用户界面。它的基本原理是一个称为 virtual DOM 的东西。virtual DOM 可以在客户端或服务端渲染，并进行通信。

在数据处理变的复杂和动态之后，客户端的 DOM 操作的性能受到影响。
React 的解决办法是：
  * 使用 virtual DOM 在服务端进行 DOM 渲染。
  * 比较真实的 DOM 和 virtual DOM，并标记两者的差异。
  * 只更新那些发生变化的 DOM 节点而不是重绘完整的 DOM 树。

React 另一个优势在于响应式组件带来了很好的重用性，React 组件库创建后可以在多个项目中共用，也能供大众使用。

一个很好的例子是 [Material－UI](http://callemall.github.io/material-ui/)，它用 React 组件实现了 Google 的 material design。

去 Instagram 看看会有更直观的感受，它的界面完全由 React JS 生成。

官网有更多的信息－[facebook.github.io/react](http://facebook.github.io/react/)

## 3. Ember.js
EmberJS 也是一个功能强大的 MVC 框架。Ember 由 Yehuda 在 2011 年发布了第一个开源版本，遵循MIT 许可。EmberJS 在创建交互应用方面可以同 Angular，React 一较高下，也拥有非常活跃的社区。

Ember 和 Angular 一样使用了双向数据绑定，也就是说，当 model 变化时更新 view；当 view 变化时更新 model，view 和 model 一直保持同步。

Ember 可以跻身优秀的框架之列，是因为它不断地增添强大的功能。它引入的 Fastboot.js 模块提供了服务端渲染的能力，这个思路有点类似 React。

Ember 想要将 Angular 双向绑定和 React 服务端渲染的优点继承过来。Ember 社区使用这种方式持续不断地给它增加优秀地功能，我十分确信 Ember 会一直流行下去。

关于更多 Ember 的信息请看 [emberjs.com](http://emberjs.com/)

## 4. Aurelia.js
AureliaJS 由 Rob Eisenberg 为首的团队创建，团队中大部分的人来自 Angular 和 Durandal。它是 Durandal 公司旗下的一个开源的产品。该公司是一个创业公司，提供下一代 web 开发所用的库，工具和框架。

AureliaJS 是 2015 年 1 月发布的，不过已经在用于生产环境。它是 Durandal 的继承者，据 Eisenberg 说其实就是 Durandal 的下一个版本。 Durandal，Angular 1还有Angualr 2 的开发者可以轻松地迁移到 Aurelia 。

虽然 AureliaJS 刚发布不久，但是如果你在寻找 JavaScript 框架，请认真考虑它。因为它底蕴深厚，并由一只非常专业的团队维护着。

AureliaJS 的模块化程度非常高，由众多相互独立的，规模较小的库组成。我们可以在项目中使用整个框架，也可以仅使用一些必备的库，或者是扩展所用包来构建自己的框架。

AureliaJS 不依赖其他的库，除了一些必要的 polyfill 没有额外的依赖。

通过官网我们可以了解更多的信息－[aurelia.io](http://aurelia.io/)

## 5. Meteor.js
喜欢只使用 JavaScript 开发完整的 web 应用吗？MeteorJS 正是这个神奇的全栈开发平台，让我们可以迅速地开发移动应用和网页应用。MeteorJS非常给力，拥有我们需要的所有功能，包括前端渲染，后端开发，业务逻辑处理和数据库管理。

Meteor 是 Meteor 开发组创造的，在 2012 年发布了一个开源版本，遵循 MIT 许可。

发布以来，MeteorJS 的生态圈迅速的发展壮大，它的社区业非常地活跃，相关的资料，教程和第三方的包很多，这些让 MeteorJS 变的非常强力。

MeteorJS 最棒的地方是仅 javaScript 开发就够了，不用花费时间去学习其他的语言。另外 MetrorJS 是模块化的，包和库可以按需加载。

服务端的代码运行在 nodejs 上，使用 MeteorJS 就能操作数据库，全都是 JavsScript， MeteorJS 是实时的 web 应用。

性能方面，数据库中的改变都会实时得反映在 UI 上，避免了不同语言间切换，减少了服务器的响应时间。

我们在官网了解到更多的信息-[meteor.com](https://www.meteor.com/)

## 6. Backbone.js
是不是想要一个轻量又包含所有功能的 JavaScript 框架呢？Backone.JS 正是这样的。 Jeremy Ashkenas 在 2010 年发布了它的一个开源版本，遵循 MIT 协议。

backbone 功能强大并应用广泛，Pinterest，Foursquare，Walmart，DIsqus 和 Delicious 这些大公司都在使用它。这仅是用户中的一小部分，由于用户数量巨大，无法罗列出来。

backbone 的优势是它比较简单，很小并容易上手。使用 backbone 开发可以说干就干。

backbone 非常灵活，因为它提供了一个最小的功能集合，开发者在此基础上二次开发，自己写代码或者使用第三方的库都可以。还可以在 backbone 的基础上构建一个全功能的框架。

过去的几个月中，backbone 使用者的增长速度缓慢，远远落后Angular， Ember 还有新出道的 React 的增速。

主要是因为 backbone 版本迭代慢，缺少其他框架提供的强大功能。

尽管还有人使用 backbone，不过更多的作为辅助框架而不是主要的框架。

Backbone 的更多信息请看 [backbonejs.org](http://backbonejs.org/)

## 7. Polymer.js
Polymer.js 是一个与众不同的框架，由 google 在 2013 年发布。Polymer 使用了 web components 来增强 HTML 的功能。

Web Components 是由 W3C 发布的一项浏览器技术，用来创建定制的 HTML 标签。比如 &lt;audio&gt; 是一个标准的 HTML5 元素，通过使用 web components 和相关的技术可以生成一个自定义的标签，比如说&lt;my-audio&gt;。

Polymer 使用的是包含 web components 在内的浏览器技术，它开创了自定义 HTML 标签的一套体系。

有关 Polymer 的更多信息请看 [polymer-project.org](https://www.polymer-project.org/)

## 8. Knockout.js
knockoutJS 由 Steve Sanderson 在 2010 年发布了第一个开源版本，遵循 MIT 许可。Knockout 依照的是 MVVM 模式，这让它变的与 Ember 还有 Angular 不太一样。

Knockout 曾经流行过，不过现在与 Angular, Ember 还有 Backbone 相比，用户增速非常缓慢。原因很简单，因为在增加新的功能和改进现有功能方面相差太多。

社区的开发者正在慢慢地转向 React 还有 Angular 的方向发展。Knockout 底蕴深厚，如果有人愿意接手它，并为它增加最新的技术，它还有希望重现辉煌。
我们可以了解更多的信息－[knockoutjs.com](http://knockoutjs.com/)

## 9. Vue.js

Vue.js 的开发者是尤小右，2014 年发布了第一个开源版本，遵循 MIT 许可。

VueJs 是个小鲜肉，吸引了很多开发者关注。VueJs 使用了 MVVM 模式，它的API 非常简单。VueJs 的设计精简至极，为开发者精心准备了几个必需的模块。

VueJs 参考了 AngularJS, ReactiveJs, konckoutJS 和 RivetsJS，使用双向的数据绑定更新 model 和 view。

目前来说，Vue 并不能同 Angular 还有 Ember 一较高下，但它很有潜力成为日后的主流框架，届时将获得相应的占有率。发布 10 个月以来它牢牢地站稳了脚跟，现在由尤小右维护。

关于 VueJs 的更多信息请看-[vuejs.org](http://vuejs.org/)

## 10. Mercury.js
我们身处 JavaScript 框架大发展的时期，这种状况前所未有。我的 top ten 名单里也给新晋者留着一个位置，这就是 Mercury.js，它刚刚发布但前景广阔。

Mercury 由Raynos 创建，遵循 MIT 许可。它迅速的火了起来，吸引了大量的开发者关注。

Mercury 应该是借鉴 react 并使用了虚拟 DOM。它是现代化的框架，完全模块化，也可以按照需要扩展。

想一下这几个概念，压缩体积小，模块化，高效还有广受欢迎，Mercury 因此占据了当今十佳 JavaScript 框架的一个位置。

了解更多请看-[githubcom/Raynos/mercury](https://github.com/Raynos/mercury)

## 总结
虽然 JavaScript 框架出现的时间不长，但在过去的几年中发展迅速。JavaScript 框架是创建复杂用户界面的首选，尤其是创建单页应用时。

不同的框架间有不同的概念和方法，但殊途同归，都在试图解决构建复杂应用时的通用问题，让单页应用变的更易用和便捷。

文章中提到的框架是当今市场中最优秀的框架。请在评论中写下你的经验和你所用的框架。

## 关于作者：

Sunil 是 noeticforce.com 的创始人和特约编辑。他的文章里包含了所有能够使移动应用，web 应用，网站变的更现代化。是一位富有激情的开发者，他使用 Python, Swift, JavaScript, PHP, Java, Android & iOS ，也包括 HTML/CSS。

如果你喜欢这个文章，可以在 Twitter 上关注 noeticforce，也可以订阅 RSS。


  [1]: https://imgs.gnux.cn/usr/uploads/2016/06/2849149145.jpg