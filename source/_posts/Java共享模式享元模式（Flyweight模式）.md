---
title: "Java共享模式享元模式（Flyweight模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","flyweight" ]
draft: false
slug: "java-model-flyweight-1"
date: "2014-10-07 13:28:00"
---

Flyweight定义：避免大量拥有相同内容的小类的开销(如耗费内存)，使大家共享一个类(元类)。
##为什么使用共享模式/享元模式

面向对象语言的原则就是一切都是对象，但是如果真正使用起来，有时对象数可能显得很庞大，比如，字处理软件，如果以每个文字都作为一个对象，几千个


<!--more-->


字，对象数就是几千，无疑耗费内存，那么我们还是要"求同存异"，找出这些对象群的共同点，设计一个元类，封装可以被共享的类，另外，还有一些特性是取决于应用(context)，是不可共享的，这也Flyweight中两个重要概念内部状态intrinsic和外部状态extrinsic之分。

说白点，就是先捏一个的原始模型，然后随着不同场合和环境，再产生各具特征的具体模型，很显然，在这里需要产生不同的新对象，所以Flyweight模式中常出现Factory模式。Flyweight的内部状态是用来共享的，Flyweight factory负责维护一个Flyweight pool(模式池)来存放内部状态的对象。

Flyweight模式是一个提高程序效率和性能的模式，会大大加快程序的运行速度。应用场合很多：比如你要从一个数据库中读取一系列字符串，这些字符串中有许多是重复的，那么我们可以将这些字符串储存在Flyweight池(pool)中。
##如何使用共享模式/享元模式

我们先从Flyweight抽象接口开始：

    public interface Flyweight{
    　public void operation( ExtrinsicState state );
    }

//用于本模式的抽象数据类型(自行设计)
`public interface ExtrinsicState { }`

下面是接口的具体实现(ConcreteFlyweight)，并为内部状态增加内存空间，ConcreteFlyweight必须是可共享的,它保存的任何状态都必须是内部(intrinsic)，也就是说，ConcreteFlyweight必须和它的应用环境场合无关。

    public class ConcreteFlyweight implements Flyweight {
    　private IntrinsicState state;
    　public void operation( ExtrinsicState state ){
    　　　//具体操作
    　}
    }

当然，并不是所有的Flyweight具体实现子类都需要被共享的，所以还有另外一种不共享的ConcreteFlyweight：

    public class UnsharedConcreteFlyweight implements Flyweight {
    　public void operation( ExtrinsicState state ) { }
    }

Flyweight factory负责维护一个Flyweight池(存放内部状态)，当客户端请求一个共享Flyweight时，这个factory首先搜索池中是否已经有可适用的，如果有，factory只是简单返回送出这个对象，否则，创建一个新的对象，加入到池中，再返回送出这个对象池。

    public class FlyweightFactory {
    　//Flyweight pool
    　private Hashtable flyweights = new Hashtable();
    　public Flyweight getFlyweight( Object key ) {
    　　Flyweight flyweight = (Flyweight) flyweights.get(key);
    　　if( flyweight == null ) {
    　　　//产生新的ConcreteFlyweight
    　　　flyweight = new ConcreteFlyweight();
    　　　flyweights.put( key, flyweight );
    　　}
    　　　return flyweight;
    　}
    }

至此，Flyweight模式的基本框架已经就绪，我们看看如何调用：

    FlyweightFactory factory = new FlyweightFactory();
    Flyweight fly1 = factory.getFlyweight( "Fred" );
    Flyweight fly2 = factory.getFlyweight( "Wilma" );
    ......

从调用上看,好象是个纯粹的Factory使用，但奥妙就在于Factory的内部设计上。
##Flyweight模式在XML等数据源中应用

我们上面已经提到，当大量从数据源中读取字符串，其中肯定有重复的，那么我们使用Flyweight模式可以提高效率，以唱片CD为例，在一个XML文件中，存放了多个CD的资料。

每个CD有三个字段：
出片日期(year)
歌唱者姓名等信息(artist)
唱片曲目 (title)
其中，歌唱者姓名有可能重复，也就是说，可能有同一个演唱者的多个不同时期 不同曲目的CD。我们将"歌唱者姓名"作为可共享的ConcreteFlyweight.其他两个字段作为UnsharedConcreteFlyweight。

首先看看数据源XML文件的内容：

    <?xml version="1.0"?>
    <collection>
    
    <cd>
    <title>Another Green World</title>
    <year>1978</year>
    <artist>Eno, Brian</artist>
    </cd>
    
    <cd>
    <title>Greatest Hits</title>
    <year>1950</year>
    <artist>Holiday, Billie</artist>
    </cd>
    
    <cd>
    <title>Taking Tiger Mountain (by strategy)</title>
    <year>1977</year>
    <artist>Eno, Brian</artist>
    </cd>
    .......
    
    </collection>

虽然上面举例CD只有3张，CD可看成是大量重复的小类，因为其中成分只有三个字段，而且有重复的(歌唱者姓名)。

CD就是类似上面接口 Flyweight：

    public class CD {
    　private String title;
    　private int year;
    　private Artist artist;
    
    　public String getTitle() {return title;}
    　public int getYear() {return year;}
    　public Artist getArtist() {return artist;}
    
    　public void setTitle(String t){title = t;}
    　public void setYear(int y){year = y;}
    　public void setArtist(Artist a){artist = a;}
    }

将"歌唱者姓名"作为可共享的ConcreteFlyweight：

    public class Artist {
    　//内部状态
    　private String name;
    
    　// note that Artist is immutable.
    　String getName(){return name;}
    
    　Artist(String n){
    　    name = n;
        }
    }

再看看Flyweight factory，专门用来制造上面的可共享的

    ConcreteFlyweight:Artist
    public class ArtistFactory {
    　Hashtable pool = new Hashtable();
    　Artist getArtist(String key){
    　　Artist result;
    　　result = (Artist)pool.get(key);
    　　////产生新的Artist
    　　if(result == null) {
    　　　result = new Artist(key);
    　　　pool.put(key,result);　　
    　　}
    　　return result;
        }
    }

当你有几千张甚至更多CD时，Flyweight模式将节省更多空间，共享的flyweight越多，空间节省也就越大。
