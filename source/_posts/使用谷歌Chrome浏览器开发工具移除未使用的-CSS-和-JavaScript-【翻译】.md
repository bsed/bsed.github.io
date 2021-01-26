---
title: "使用谷歌Chrome浏览器开发工具移除未使用的 CSS 和 JavaScript 【翻译】"
categories: [ "Css" ]
tags: [ "chrome","unused css","unused js" ]
draft: false
slug: "remove-unused-css-and-ja-x-vasc-x-ript-using-google-chrome-development-tools-1"
date: "2019-11-26 14:48:00"
---

与图像一样，CSS和JavaScript是导致代码膨胀和页面加载缓慢的两个最常见原因。有时(虽然不经常)这是不可避免的，页面的功能和设计需要大量的代码，但是几乎总是有改进的空间。用户不喜欢等待页面加载，搜索引擎知道这一点。你应该尽力提高页面速度。


尤其令人沮丧的是，在某些页面中，一个页面所调用的大部分脚本是冗余的，并且实际上没有被用于任何用途。出现这种情况的原因有很多，其中包括以下几点:

-  在模板的开头部分页面需要调用脚本，但实际情况，站点上的每个页面都需要调用脚本（这并不是必须的）。 
-  仅在与页面的特定部分进行交互时使用的脚本和样式，例如选项卡，手风琴，多面导航 。
-  Cruft是随着时间的推移而构建的，其中随着时间的推移会添加额外的功能或样式，而不需要检查现有的代码以查看是否可以修改它以适应新的需求( Dawn Anderson recently talked about the concept of [generational cruft](https://www.slideshare.net/DawnFitton/technical-seo-generational-cruft-in-seo-there-is-never-a-new-site-when-theres-history-brighton-seo-concise-deck) ) 
-  CMS安装插件/扩展，其中只有一小部分功能是真正需要的。
-  糟糕的代码。


<!--more-->


有许多其他原因可以解释为什么有这么多不使用的代码残留，但这些是一些常见的原因。你可以在这里阅读更多关于这个主题的[文章](https://edit.co.uk/blog/identifying-unused-css-and-javascript-with-google-chrome-dev-tools/Include%20caveat%20based%20on%20https:/css-tricks.com/heres-the-thing-about-unused-css-tools/)

## 我们如何识别哪些是被使用的，哪些不是?

有各种方法来识别未使用的代码，包括浏览器插件，自定义测试脚本，第三方库等，但是在Chrome开发人员工具中也有一种非常简便的方法来实现此目的，这就是我们今天要探讨的重点。

要做的第一件事是在Chrome中打开要检查的页面，然后按F12调出开发人员工具。 打开“ Chrome开发者工具”窗口后，按住CTRL + SHIFT + P打开下面所示的过滤器栏。
![chrome_unused_code_01.jpg][1]

不管你在哪个选项卡(元素、控制台、源等等)都无关紧要， 在过滤器框中，输入`coverage`，它将显示如下所示的选项。

![chrome_unused_code_02.jpg][2]

您应该选择第一个选项，即绘制显示覆盖率（`Drawer Show Coverage`），它将为您显示两个面板，如下所示。

![chrome_unused_code_03.jpg][3]

在任一面板中，单击重新加载按钮(`Click to reload button`)，这将刷新页面并开始向底部面板填充数据，如下例所示。
![chrome_unused_code_04.jpg][4]


除了页面上加载的所有脚本的URL（此处未显示，但通常会显示在最左侧）之外，我们还得到脚本的类型，文件的总大小和未使用的字节数。 右侧的条显示了已使用和未使用代码之间的划分，红色部分显示了百分比的可视表示。

在屏幕的左下角，您还将看到页面上加载的未使用代码的总百分比。
![chrome_unused_code_05.jpg][5]


从上面的例子中可以看出，2.3 MB的CSS和JS 1.5 MB(64%)在初始加载时没有在这个特定的页面上使用。通过单击底部窗格中的单个行，您还可以看到顶部页面中没有使用的特定代码块。
![chrome_unused_code_06.jpg][6]

也可以使用[Headless Chrome](https://developers.google.com/web/updates/2017/04/headless-chrome)和[Puppeteer](https://developers.google.com/web/tools/puppeteer/)获取此信息。 您可以在此处找到有关此内容的[更多信息](http://blog.cowchimp.com/monitoring-unused-css-by-unleashing-the-devtools-protocol/)。这是一种更复杂的方法，但是如果您需要批量获取数据，则可伸缩性更高。 我们将在以后的文章中进一步讨论这种方法。


## 我该如何处理这些Unused CSS /Unused JS

令人振奋的是，可以如此快速，轻松地查看该页面上未使用的代码(`Unused CSS/Unused JS`)，但记住前面提到的一些要点，而不只是告诉开发团队删除所有突出显示的代码，**这很很重要**（希望他们说不，如果您 还是这样做了！）。

要了解脚本不可用的影响，您可以通过阻塞资源然后与页面进行交互以识别UI问题，逐页进行测试。 您可以在这里找到[更多信息](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage)

下面我们将讨论一些方法，您可以使用这些信息来提高站点上的编码效率。

## 推迟非关键脚本和样式

除了删除完全冗余的脚本外，识别初始页面加载时未使用的代码的其他常见原因之一是，尽量减少在用户与页面交互之前不会实际执行任何操作的[render blocking code](https://varvy.com/pagespeed/render-blocking.html)。 前面提到的示例包括手风琴，标签，搜索功能等。

诸如Lighthouse之类的工具会同时突出显示渲染阻止脚本和未使用的CSS，但会返回文件名而不是未使用的脚本特定部分，但是很可能脚本的某些部分是初始渲染所必需的，而某些部分仅在交互时使用 。

通过查看更细粒度的级别，可以将代码拆分为在页面加载过程开始时解析的代码和未使用的脚本和样式，这些脚本和样式可以延迟到上述fold内容加载之后。

## 为不同类型的页面使用不同的模板

许多站点在站点的每个页面上都包含对所有外部脚本的引用，这通常是未使用代码的原因。 例如，一个电子商务站点可能正在主页和其他不相关页面上加载产品清单，搜索结果，购物篮等的所有脚本和样式。

在这种情况下，我们建议为网站的每个部分使用不同的模板，以确保在任何时候仅加载所需的功能和样式。 另一种选择是使用[Google Tag Manager](https://tagmanager.google.com/#/home)根据页面上的选择器有条件地注入脚本引用，但这通常不是最佳方法，而通常优选基于模板的方法。


## 使用脚本库的自定义构建

使用第三方库(如JQuery和BootStrap)是另一个经常导致代码膨胀的原因。当站点只使用可用功能的一小部分时，它们常常会加载整个库。

有许多选项可用于构建这些库的自定义版本，以最小化所包含的未使用代码的数量。你可以在这里找到一个Bootstrap定制程序，在这里找到一个jQuery构建器，在这里找到一个jQuery UI构建器

您还应该考虑是否真的需要加载外部库。有时候，使用简单的定制解决方案可以节省大量的成本。

原文：[https://edit.co.uk/blog/identifying-unused-css-and-javascript-with-google-chrome-dev-tools/](https://edit.co.uk/blog/identifying-unused-css-and-javascript-with-google-chrome-dev-tools/)

  [1]: https://imgs.gnux.cn/usr/uploads/2019/11/545033079.jpg
  [2]: https://imgs.gnux.cn/usr/uploads/2019/11/3893453668.jpg
  [3]: https://imgs.gnux.cn/usr/uploads/2019/11/250838940.jpg
  [4]: https://imgs.gnux.cn/usr/uploads/2019/11/815689163.jpg
  [5]: https://imgs.gnux.cn/usr/uploads/2019/11/4280276108.jpg
  [6]: https://imgs.gnux.cn/usr/uploads/2019/11/236629856.jpg