---
title: Markdown+Pandoc
date: 2014-08-28 12:35:00
updated: 2014-08-28 13:09:19
tags: 
- idea
categories: 
- linux

---
`Markdown+Pandoc`，可以把自己的写作内容，变成世界上已有的任何格式的文件，包括很炫的slide，html5。没有人（或者我没看到）总结过这些内容，导致我走了很多弯路才最终打通任督二脉，特此纪念。
了解Markdwon以后，我的写作世界，只有它；看到Pandoc格式转换以后，对生成的slide和pdf羡慕的不行。那时，自己期望以后的写作是这样的：首先用Markdown把自己的想法写下来；其次，通过Pandoc，把写好的Markdown文件，转换成Slide或者PDF。如此而已。
但是，我一直对于pandoc不知道怎么去使用。问了一些人，查了很多网页，都没有写如何去使用pandoc，直到，我在[github](https://github.com/ "github")上搭建Blog，安装[Ruby](http://www.ruby-lang.org/ "ruby-lang")，以及安装了[Jekyll](https://github.com/jekyll/jekyll "jekyll")，才最终发现我会使用Pandoc了。可见，机遇是给有准备的人。
这周准备R的分享，


<!--more-->


特意尝试用Markdown和Pandoc准备资料。用Markdown写了10分钟的内容，然后用Pandoc“秒杀”了一个Slide的Html的文件，效果不错。
鉴于个人对于过程和效果的喜欢，特别整理，分享给有缘人。 个人估计，有缘人看到以后，必定会引起写作界的一波学习热！
# 一、Markdown使用 #

[Markdown](http://wowubuntu.com/markdown/)，就像一个人的任脉。它简洁，方便，只要集中精力写文章就好，排版不用管。它的语法如此简单，简单的连一个初中生就可以10分钟学会去用，5分钟写一个例子，就会了。此言真不虚。比如，我要写一篇文章,可以这么写：

    #我的工作
    - 工作在Etao 我属于阿里妈妈，是的。你没看错，就是阿里妈妈。
    - 工作是BI  我的工作是BI.BI？啥？就是。。。就是。。那个。 
    - 工作薪水 薪水？老板不让说。不告诉你。

展现的样式呢，是这样的：
#我的工作
- 工作在Etao 我属于阿里妈妈，是的。你没看错，就是阿里妈妈。
- 工作是BI  我的工作是BI.BI？啥？就是。。。就是。。那个。 
- 工作薪水 薪水？老板不让说。不告诉你。

# 二、Pandoc使用 #

`Pandoc`，这个不知道怎么发音，google也没找到。好吧，我就读做panda吧，谁让它是国宝。
Pandoc的运行，是在命令行里面。可是，没那么简单，不是任何一个cmd都可以。你必须要下载[Pandoc](http://johnmacfarlane.net/pandoc/installing.html "Pandoc")，请参考这里.根据自己的os，选择Windows 或者其他。
安装以后，记得Pandoc的目录是啥，然后再到cmd里面去操作一些失传已久的doc命令，转换到pandoc的路径下。
我个人习惯，是把要转换的文件，比如test.md，放到pandoc的路径下，这样在使用pandoc转换的时候，不用输入太多的路径（尤其是我们很多路径是中文，怕可能有一些问题）。当然，也可以调用其他路径的文件，只要自己觉得舒服。
pandoc，就像linux下的iconv，可以把其他格式的文件，转化成自己想要的格式。具体的格式参考请看这里。
个人常用的有两个格式转换：

        a>md文件转换成html5
        pandoc -s --mathml -i -t dzslides test.md -o test.html
        b>md文件转换成pdf
        pandoc -t beamer test.md -o test.pdf 

这里强调一点，如果想转成PDF文件，要安装LATEX。推荐安装MiKTex。但是，中文转PDF，因latex支持中文差，转换有问题。对于Latex熟悉的人，可以参考这个，看是否能解决中文转slide pdf的问题。
文件转换完成以后，如果有一些地方不合适，可以调整原始的md文件，再转换一次。等熟练以后，从写，到转换就非常迅速了。 当然，Pandoc还有很多的转换格式，大家可以自己去研究发觉。
记得有好的东西，要分享。 

参考资料：
-
1.[http://wowubuntu.com/markdown/](http://wowubuntu.com/markdown/)  
2.[http://johnmacfarlane.net/pandoc/installing.html](http://johnmacfarlane.net/pandoc/installing.html)  
3.[http://johnmacfarlane.net/pandoc/demos.html](http://johnmacfarlane.net/pandoc/demos.html)  

*软件下载：*  
1.[http://222.76.215.233:81/soft/MarkdownPad_XP85.rar ](http://222.76.215.233:81/soft/MarkdownPad_XP85.rar "MarkdownPad")  
2.[http://www.markdownpad.com/](http://www.markdownpad.com/ "markdownpad2") `官方`  
3.[http://johnmacfarlane.net/pandoc/installing.html
](http://johnmacfarlane.net/pandoc/installing.html "Pandoc")  