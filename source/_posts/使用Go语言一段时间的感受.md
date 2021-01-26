---
title: "使用Go语言一段时间的感受"
categories: [ "Golang" ]
tags: [ "golang","feels" ]
draft: false
slug: "golang-use-feels"
date: "2014-11-19 22:14:00"
---

有一段时间没更新了。最近在忙一个Server+Client的项目，Client是Android手机，大概也就是几十的规模。Server是纯Golang实现，没有apache或者ngix这种web server，也没有数据库，自己写了个文件管理module，handle这种小规模的服务没问题。算下来接触Golang也有四个多月了，断断续续写了一些东西，这里纪录一下心得吧

先大概说下为什么用Golang。我是一个对语言有洁癖的人，曾经是一个c+Python的坚定呐喊者，最常说的一句话就是，只要熟练这两种，什么情况都能应付，根本不用Java和C++(纯指我所在的领域）。核心代码用c，速度快，需要记的语言细节少；外围用Python glue，灵活，简洁，任何模块都容易上手，绝配。Java的繁琐，C++的无数无用的特性，都让我只在不得不用的时候才去用。Objective-C是另一个我欣赏的语言，问题是不跨平台，过于封闭。


<!--more-->


可惜的是，在这个节奏极快的时代，不是所有情况下都适合上c。之前有一个项目也是类似的架构和规模，为了节省时间，当初几乎没有服务器平台编程经验的我，在服务器端选择用Django+Apache+MySQL做，成熟，社区活跃，又是python作为主要开发语言，这些都是这个选择的原因。说实话，几个月过去后，回首看，这不是一个愉快的经历。Django是一个好架构，大而全，而大而全有时也就意味着臃肿，五花八门的配置，过紧的模块耦合对引入第三方工具限制颇多，自带的ORM又不好用。之前从来没有搞过服务器配置的我，对Apache的配置和效率所带来的琐碎的东西也头疼。总的来说这个部分花了我很多时间，有新手学习服务器编程的必经过程，也有折腾Django和Apache没必要的时间浪费，很大部分上抵消了Python带来的快速开发的灵活性。而一旦服务器上线，动态语言带来的一些bug又会让人头疼。对于普通高校实验室这种没有完善的服务器调试的条件，基本就是改了就上线用，有些隐蔽bug到某些条件分支才会触发，一旦在运行中途出问题，改起来也麻烦。

从那时起，我就特别想，要是有一种语言能把c和Python的优点结合起来，也就是说

 - 速度快，高性能
 - 简洁明了，需要记的语言细节少，开发迅速(c)
 - 灵活，开发快速，类Python的list，map等常用数据结构支持(Python)
 - 完善的模块支持，模块也容易上手(Python)
 - 对程序员友好的并行架构(Erlang)
 - 安全，绝大部分问题能消灭在compile time中(C minus pointer)

那基本就是系统级和网络级编程最对我胃口的语言了。然后我就找到了Go。

Golang是一个新语言，截至目前为止，第一版正式版还没有发布。Golang的设计者是Robert Griesemer, Rob Pike和Ken Thompson，当年设计C和Unix，后来的Plan9团队中的人 。Golang的设计理念很明确，就是将动态类型语言的编程容易度和静态类型语言的安全效率结合起来。如果你想更深入了解Golang的发展历史以及完整的目标，请参考[Golang FAQ](http://golang.org/doc/go_faq.html#What_is_the_purpose_of_the_project)。

当然，Golang吸引我的地方，不是因为其是Google出品，也不是因为其设计者皆为大牛，而是因为，Golang真的做到了它所宣称的目标。Golang就如同C和Python中间的完美结合，如果你是Python爱好者，又追求代码的速度和并行化，那么简单说，Golang就是为你设计的。Golang有很浓厚的C的遗风，尽量屏蔽C++和Java的影响，比如没有独立的OO体系（并不是说不能OO），一切以struct为中心，没有exceptions(Oh yes!)，仍然有指针，等等。但是，Golang又吸取了很多新语言的精华，并带有自己独特的设计。比如

1. 保留但大幅度简化指针

Golang保留着C中值和指针的区别，但是对于指针繁琐用法进行了大量的简化，引入引用的概念。所以在Golang中，你几乎不用担心会因为直接操作内寸而引起各式各样的错误。

2. 多参数返回

还记得在C里面为了回馈多个参数，不得不开辟几段指针传到目标函数中让其操作么？在Go里面这是完全不必要的。而且多参数的支持让Go无需使用繁琐的exceptions体系，一个函数可以返回期待的返回值加上error，调用函数后立刻处理错误信息，清晰明了。

3. Array，slice，map等内置基本数据结构

如果你习惯了Python中简洁的list和dict操作，在Golang中，你不会感到孤单。一切都是那么熟悉，而且更加高效。如果你是C++程序员，你会发现你又找到了STL的vector 和 map这对朋友。

4. Interface

Golang最让人赞叹不易的特性，就是[interface的设计](http://research.swtch.com/interfaces)。任何数据结构，只要实现了interface所定义的函数，自动就implement了这个interface，没有像Java那样冗长的class申明，提供了[灵活太多的设计度和OO抽象度](http://golangtutorials.blogspot.com/2011/06/interfaces-in-go.html)，让你的代码也非常干净。千万不要以为你习惯了Java那种一条一条加implements的方式，感觉还行，等接口的设计越来越复杂的时候，无数Bug正在后面等着你。

同时，正因为如此，Golang的interface可以用来表示任何generic的东西，比如一个空的interface，可以是string可以是int，可以是任何数据类型，因为这些数据类型都不需要实现任何函数，自然就满足空interface的定义了。加上Golang的type assertion，可以提供一般动态语言才有的duck typing特性， 而仍然能在compile中捕捉明显的错误。

5. OO

Golang本质上不是面向对象语言，它还是过程化的。但是，在Golang中， 你可以很轻易的做大部分你在别的OO语言中能做的事，用更简单清晰的逻辑。是的，在这里，不需要class，仍然可以继承，仍然可以多态，但是速度却快得多。因为本质上，OO在Golang中，就是[普通的struct操作](http://golangtutorials.blogspot.com/2011/06/structs-in-go-instead-of-classes-in.html)。

6. Goroutine

这个几乎算是Golang的招牌特性之一了，我也不想多提。如果你完全不了解Goroutine，那么你只需要知道，这玩意是超级轻量级的类似线程的东西，但通过它，你不需要复杂的线程操作锁操作，不需要care调度，就能玩转基本的并行程序。在Golang里，触发一个routine和erlang spawn一样简单。基本上要掌握Golang，以Goroutine和channel为核心的[内存模型](http://golang.org/doc/go_mem.html)是必须要懂的。不过请放心，真的非常简单。

7. 更多现代的特性

和C比较，Golang完全就是一门现代化语言，原生支持的Unicode, garbage collection, Closures(是的，和functional programming language类似), function是first class object，等等等等。

看到这里，你可能会发现，我用了很多轻易，简单，快速之类的形容词来形容Golang的特点。我想说的是，一点都不夸张，连Golang的入门学习到提高，都比别的语言门槛低太多太多。在大部分人都有C的背景的时代，对于Golang，从入门到能够上手做项目，最多不过半个月。Golang给人的感觉就是太直接了，什么都直接，读源代码直接，写自己的代码也直接。

有朋友要抗议了，你把Golang吹的这么好，难道它就没有缺点？有，当然有，不过和它的优点比，我觉得很多缺点都是因为整个语言太新，不成熟，随着时间的推移都能得到解决，相比之下都能忍了。如果你希望进一步了解Golang的优缺点，可以参考以下yufeng写的这篇文章，系统编程语言明日之星—Go（http://blog.yufeng.info/Go.pdf。

还有朋友要说，Golang这么好，为什么没人用？我想说，眼界放开点，这个世界精彩的东西比你想象的多。Golang被Google用于[Youtube的数据库](http://code.google.com/p/vitess/)，被[越来越多的国外公司](http://go-lang.cat-v.org/organizations-using-go)(大部分创业公司)用于后端开发，甚至在天朝，也有完全用Golang做服务开发的[云应用公司](https://qbox.me/)了。可以说，随着Go 1即将到来的正式推出，Golang的使用范围，应该会越来越广。

好，总结时间

如果你是Python和动态语言狂热爱好者，Go不一定能给你带来很大的惊喜，这纯粹取决于你得项目性质，考虑到Python目前在很多地方都用C做核心运算，速度在大部分情况下都不是大问题。scalability是一个问题，但并不是人人都会遇到的。

如果你是C爱好者，强烈建议你学习和使用Go。Go可以调用C/C++程序，又提供了太多的便利，速度上稍有牺牲，但并不大。在绝大部分场景下Go能给你带来媲美C的性能，而对于某些确实性能过于关键的场合，也可以通过cgo让Go和C搭配。

如果你是Java爱好者，除非你是做Android这种不得不用Java的平台，否则也建议你尝试学习Go，这个开发上感觉的差异如同比较开载着1吨石头的拖拉机和开保时捷911那么明显，而Java能给你的，Go能给得更好。

如果你是C++爱好者，!@#$%^&*，恭喜你，至少你的智商应该是没问题的。人生苦短，赶紧脱离C++这个苦海吧。你用来学89个C++高级特性的时间，估计已经用Go写了64个开源项目了。

如果你是像我一样的C和Python的爱好者，对动态语言又没有特殊的热爱……我还需要说什么呢？

让我们荡起双桨，去遨游[Golang的海洋](http://golang.org/doc/install.html)吧。