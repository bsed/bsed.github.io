---
title: "用Java解析RSS实例"
categories: [ "Java" ]
tags: [ "rome","jdom","rss" ]
draft: false
slug: "use-java-parse-rss"
date: "2014-12-19 16:53:00"
---

##  前言  ##
ava解析rss的方法有很多，这里使用开源java库rome解析rss。
##  ROME  ##
ROME是一组Atom/RSS工具类它使得用Java来操作大部份syndication格式变得更加容易。ROME可能是目前最完善的开源聚合工具，ROME支持如下协议：


<!--more-->


RSS 0.90
RSS 0.91 Netscape
RSS 0.91 Userland
RSS 0.92
RSS 0.93
RSS 0.94
RSS 1.0
RSS 2.0
Atom 0.3
Atom 1.0
##  依赖jar包  ##

 - rome.jar 可到http://wiki.java.net/bin/view/Javawsxml/Rome下载最新的
 - jdom.jar

##  代码实例  ##

    package feedly.index;
    import java.net.URL;
    import java.util.List;
    import com.sun.syndication.feed.synd.SyndCategory;
    import com.sun.syndication.feed.synd.SyndContent;
    import com.sun.syndication.feed.synd.SyndEnclosure;
    import com.sun.syndication.feed.synd.SyndEntry;
    import com.sun.syndication.feed.synd.SyndFeed;
    import com.sun.syndication.io.SyndFeedInput;
    import com.sun.syndication.io.XmlReader;
    public class TestParse {
        public static void main(String[] args) {
            TestParse test = new TestParse();
            test.parseRss();
        }
        public void parseRss() {
            
            String rss = "http://iyanlei.com/feed";
            try {
                URL url = new URL(rss);
                // 读取Rss源   
                XmlReader reader = new XmlReader(url);
                
                System.out.println("Rss源的编码格式为：" + reader.getEncoding());
                SyndFeedInput input = new SyndFeedInput();
                // 得到SyndFeed对象，即得到Rss源里的所有信息   
                SyndFeed feed = input.build(reader);
                //System.out.println(feed);
                // 得到Rss新闻中子项列表   
                List entries = feed.getEntries();
                // 循环得到每个子项信息   
                for (int i = 0; i < entries.size(); i++) {
                    SyndEntry entry = (SyndEntry) entries.get(i);
                    // 标题、连接地址、标题简介、时间是一个Rss源项最基本的组成部分   
                    System.out.println("标题：" + entry.getTitle());
                    System.out.println("连接地址：" + entry.getLink());
                    SyndContent description = entry.getDescription();
                    System.out.println("标题简介：" + description.getValue());
                    System.out.println("发布时间：" + entry.getPublishedDate());
                    // 以下是Rss源可先的几个部分   
                    System.out.println("标题的作者：" + entry.getAuthor());
                    // 此标题所属的范畴   
                    List categoryList = entry.getCategories();
                    if (categoryList != null) {
                        for (int m = 0; m < categoryList.size(); m++) {
                            SyndCategory category = (SyndCategory) categoryList.get(m);
                            System.out.println("此标题所属的范畴：" + category.getName());
                        }
                    }
                    // 得到流媒体播放文件的信息列表   
                    List enclosureList = entry.getEnclosures();
                    if (enclosureList != null) {
                        for (int n = 0; n < enclosureList.size(); n++) {
                            SyndEnclosure enclosure = (SyndEnclosure) enclosureList.get(n);
                            System.out.println("流媒体播放文件：" + entry.getEnclosures());
                        }
                    }
                    System.out.println();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

文章参考：[http://zmfkplj.iteye.com/blog/461398](http://zmfkplj.iteye.com/blog/461398)
