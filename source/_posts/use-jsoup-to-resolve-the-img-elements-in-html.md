---
title: 使用Jsoup解析出html中的img元素
date: 2015-09-17 22:16:00
updated: 2017-02-06 22:19:46
tags: 
- mysql
- Sequelize
categories: 
- js

---
`jsoup` 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。

这里的话，有两种方案，第一种 是在后台处理，把解析好的图片存集合里，然后转发到页面，遍历显示；还有一种是把博客内容送到页面，用Jquery处理。

我考虑了下。还是在后台处理好比较好，因为项目本身用到了 cnd加速，所以在前端处理反而影响效率。

在后端处理，可以使用Jsoup，很方便；

Jsoup主页：http://jsoup.org/


<!--more-->


maven地址：
```xml
<dependency>
  <groupId>org.jsoup</groupId>
  <artifactId>jsoup</artifactId>
  <version>1.8.3</version>
</dependency>
```

这里给下关键代码：
```java
for(Blog blog:blogList){
    List<String> imagesList=blog.getImagesList();
    String blogInfo=blog.getContent();
    Document doc=Jsoup.parse(blogInfo);
    Elements jpgs=doc.select("img[src$=.jpg]"); //　查找扩展名是jpg的图片
    for(int i=0;i<jpgs.size();i++){
      Element jpg=jpgs.get(i);
      imagesList.add(jpg.toString());
      if(i==2){
        break;
      }
    }
}
```

我自己使用的工具类