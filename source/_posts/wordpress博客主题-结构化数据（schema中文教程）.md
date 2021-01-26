---
title: "wordpress博客主题 结构化数据（schema中文教程）"
categories: [ "PHP" ]
tags: [ "wordpress","schema","seo" ]
draft: false
slug: "wordpress-blog-theme-structured-data-schema-chinese-tutorial"
date: "2015-08-12 15:11:00"
---

![schema-org-mircrodata-improve-seo.png][1]
Google、Microsoft和Yahoo!约定共同提出一种以Microdata格式为基础的通用标记词汇集——Schema.org。Schema.org 是一系列可扩展的 `schemas` 标记集合，站长可以使用这些标记嵌入格式化数据到网页中，从而使得搜索引擎更容易识别网页内容。
Schema.org拥有丰富的词汇集，涵盖了Microdata、Microformats和RDFa当前定义的全部项目，不过它将所有类型都规定为[DataType](http://www.schema.org/DataType)和[Thing](http://www.schema.org/Thing)的子类型，全部通过以下HTML属性定义：`itemscope`、`itemtyp`e和`itemprop`。
先看没有按照 Schema.org 结构化数据的html代码

    <article>
      <header>
        <h1>A blog post</h1>
        <div>
          Posted on <time datetime="2012-01-19T12:59:46+00:00" pubdate>January 19, 2012</time>
        </div><!-- .entry-meta -->
      </header><!-- .entry-header --> 
      <div> ... Entry goes here ... </div><!-- .entry-content --> 
      <footer>
        Category: <span><a href="#" rel="category">Programming</a></span>
        Tagged: <span><a href="#" rel="tag">javascript</a></span>
        By: <a href="#" rel="author">Joshua</a>.
      </footer><!-- .entry-meta -->
    </article>


<!--more-->


使用 Schema.org 结构化数据 的html代码：

    <article itemscope itemtype="http://schema.org/BlogPosting">
      <header>
        <h1 itemprop="headline">A blog post</h1>
        <div>
          Posted on <time datetime="2012-01-19T12:59:46+00:00" pubdate itemprop="datePublished">January 19, 2012</time>
        </div><!-- .entry-meta -->
      </header><!-- .entry-header -->
    
      <div itemprop="articleBody"> ... Entry goes here ... </div><!-- .entry-content -->
    
      <footer>
        Category: <span itemprop="articleSection"><a href="#" rel="category">Programming</a></span>
        Tagged: <span itemprop="keywords"><a href="#" rel="tag">javascript</a></span>
        By: <span itemprop="author" itemscope itemtype="http://schema.org/Person">
          <span itemprop="name">
            <a href="#" rel="author" itemprop="url">Joshua</a>
          </span>
        </span>.
      </footer><!-- .entry-meta -->
    </article>

使用 Schema.org 结构化数据的结果怎么样：

使用google提供的结构化数据测试工具测试一下：
http://www.google.com.hk/webmasters/tools/richsnippets
这个工具可能会由于众所周知的原因，非常缓慢，或者测试不了，请想办法科学测试。
schema 结构化数据 例子使用Google结构化数据测试工具的结果如下：

    Item
        Type: http://schema.org/blogposting
        headline = A blog post
        datepublished = 2012-01-19T12:59:46+00:00
        articlebody = ... Post content here ...
        articlesection = Programming
        keywords = javascript
        author = Item (1)
    
    Item 1
        Type: http://schema.org/person
        name = Joshua
        url
            text = Joshua
            href = #

我们的外国友人根据 schema 结构化数据 规则，对wordpress 默认模版 Twenty-Eleven 和 Twenty-Twelve 进行了 schema 结构化数据 处理，大家可以参考学习。
Twenty-Twelve-Schema.org-Child-Theme 下载地址：
https://github.com/jlyman/Twenty-Eleven-Schema.org-Child-Theme
Twenty-Twelve-Schema.org-Child-Theme下载地址：
https://github.com/jlyman/Twenty-Twelve-Schema.org-Child-Theme


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/1972264385.png