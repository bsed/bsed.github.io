---
title: Guava – Google的Java常用类库
date: 2014-09-05 12:54:00
updated: 2014-09-05 12:55:35
tags: 
- typecho
- sql
categories: 
- sql

---
[Guava][1]的前身是Google Collections，是Google开源出来的一个Java常用类库，包含了一些集合的便捷操作API。从Google Collections进化到Guava后，对常用的字符串操作、文件操作、网络操作、多线程并发操作提供了很方便的API，当然重头戏还是集合部分，下面会用一些例子来梳理一下Guava的常用API。

Guava目前的版本（release 09）主要分这么几个包：

> com.google.common.base com.google.common.annotations
> com.google.common.primitives com.google.common.io
> com.google.common.net com.google.common.collect
> com.google.common.util.concurrent

先从com.google.common.base说起，说白了，这个包下的类，就是对原生的java.lang下的类进行一些增强，很多常用的操作，省的自己写冗余代码了，Guava直接给你提供现成的了。


<!--more-->


最常用的：Strings.isNullOrEmpty(…)，呵呵，虽然没啥技术含量，但是用惯了JavaScript、PHP之类的动态语言，对Java原本那种啰嗦的写法真是越看越不顺眼。。。

再比如字符串匹配操作：CharMatcher.anyOf(“aeiou”).countIn(“This is a test!”); // 元音字母计数，用for循环写得拖沓很多了吧。

拼字符串：String s = Joiner.on(“,”).skipNulls().join(userIdArray); // 链式操作，简单明了，如果用Java老老实实自己写就得这样了：

    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < userIdArray.length; i++) {
        if (userIdArray[i] != null)
            sb.append(userIdArray[i]).append(",");
    }
    if (sb.length() > 0)
        sb.deleteCharAt(sb.length() - 1);
    String s = sb.toString();

孰优孰劣。。。再来个更强的：String s = Joiner.on(“;”).withKeyValueSeparator(“|”).join(userIdNameMap); // 你一定遇上过这样的需求，有一组用户ID和用户昵称的对照表，要拼一个字符串，ID和昵称之间用|分隔，每对记录之间用;分隔，又是简单一句链式操作搞定，自己写又得双层for循环咯。。。

与拼字符串相对应的还有拆字符串：Splitter.on(“|”).trimResults().omitEmptyStrings().split(” a,,b , c ,”); // 如你所愿的会返回a、b、c这3个元素，可以去掉左右空白字符、忽略空字符串，自己写又得for循环 + 一堆if/else了吧。。。

再来看看com.google.common.io包，主要包括文件操作和IO操作。原本的Java API，对IO操作有太多的装饰者模式，一层套一层，还有各种Exception，得写很多try..catch..finally，很烦躁。再来看看Guava提供的API：Files.touch(…) ，更新文件的时间戳；Files.deleteRecursively(…) ，递归删除文件夹及里面所有文件；List<String> Files.readLines(File, Charset) ，指定文件和字符集，把内容读到一个List中；Files.append(CharSequence from, File to, Charset) ，把字符串用指定字符集追加到文件尾部。

想一想，手写Java代码需要多少工作：new一层又一层的InputStream、Reader，try..catch..finally，最恶心的是finally里面还要再try..catch。

重头戏来了，com.google.common.collect包，是对Java Collections的一次升级，附加了很多非常好用的API。

Guava新增了一个概念，叫做 不可变集合，ImmutableCollection，很多情况下，我们使用List、Map、Set，只是作为一种DTO，仅仅是传输数据，而它的size实际上在编码时就是已知确定的，这时我们使用不可变集合，可以有更好的性能和更方便的API。比如说，用Hibernate时，HQL语句需要绑定变量：from Student s where s.name=:name and s.age>:age，我们需要传一个Map作为参数，可以这样：Map params = ImmutableMap.of(“name”, “zhangsan”, “age”, 20); 就得到了一个不可变Map对象。而用标准Java API，至少需要写3行代码。

原先需要new一个Map需要这么写：Map<String, String> aMap = new HashMap<String, String>(); 其实Java的泛型支持合理的类型推断的，Guava就提供了这样的API：Map<String, String> aMap = Maps.newHashMap(); // DRY – don’t repeat yourself! 而且还用不着@SuppressWarnings(“unchecked”)，很方便，有Lists、Sets、Maps等工厂类。

非常实用的一个API： MapDifference<K, V> diff = Maps.difference(Map<K, V> left, Map<K, V> right); 之后可以调用
diff.entriesOnlyOnLeft() // 获得只在左集合有的部分
diff.entriesOnlyOnRight() // 获得只在右集合有的部分
diff.entriesInCommon() // 获得交集部分

Guava新增了一个概念，叫做BiMap，双向Map，比如说我需要一个Map，可以根据用户ID找到用户昵称，还可以根据用户昵称查找用户ID，这时就可以用到双向Map了。

传统的Set是保存不重复的元素的，而Guava有个Multiset，可以对每个元素进行计数，通俗点讲，Multiset<E>就相当于Map<E, Integer>，每当对Multiset add一个元素，就对这个元素的计数值+1，一个元素可以添加多次。类似的还有Multimap，Multimap<K, V>就相当于Map<K, Collection<V>>，每当put一对KV，就把V放到K对应的Collection中去，对于同一个Key，可以put多个不同的Value。

总结

可以说，Guava本身并没有什么高科技，它的实现都很好理解，大家也都能自己实现出来一个。但是它的API确实是太实用了，让人有一种“对，我就是想要一个这样的接口！”的感觉，用起来非常痛快，强烈推荐给所有Java开发者。

对于一个熟练的工程师来说，很多时候需要的不是神秘、高科技的重型工具，只是一把趁手的螺丝刀。


  [1]: http://code.google.com/p/guava-libraries/