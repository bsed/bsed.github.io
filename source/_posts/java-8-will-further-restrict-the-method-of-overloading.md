---
title: Java 8将进一步限制重载的方法引用
date: 2015-08-17 08:41:00
updated: 2015-08-18 09:02:51
tags: 
- love
categories: 
- default

---
方法重载 一直是一个主题与复杂的感情。 我们博客,它引入了几次的警告:

你会后悔用Lambdas进行重载
“Don't Repeat Yourself”:方法重载
[为什么大家都讨厌操作符重载](http://blog.jooq.org/2014/02/10/why-everyone-hates-operator-overloading/)
[API设计师,要小心](http://blog.jooq.org/2014/05/16/java-8-friday-api-designers-be-careful/)
重载是有用的有两个主要原因:

允许带参数
允许带可供选择的参数
机器人的原因是出于简单为API提供方便消费者。 很好的例子是JDK容易找到:

### 默认参数

    public class Integer {
        public static int parseInt(String s) {
            return parseInt(s,10);
        }
    
        public static int parseInt(String s, int radix) {}
    }

在上面的示例中,第一个 parseInt() 方法是一个简单方便的方法调用第二个最常用的基数。

<!--more-->

### 允许带可供选择的参数

有时,类似的行为可以通过使用不同类型的参数,这意味着类似的事情但不兼容于Java的类型系统。 例如当构造 字符串 :

    public class String {
        public static String valueOf(char c) {
            char data[] = {c};
            return new String(data, true);
        }
    
        public static String valueOf(boolean b) {
            return b ? "true" : "false";
        }
    
        // and many more...
    }

正如您可以看到的,根据参数类型优化方法。 这并不影响该方法的“感觉”阅读或编写源代码时的语义 返回对象的值() 方法是相同的。

该技术的另一个用例是常用的,类似但不兼容的类型需要方便彼此之间的转换。 作为一个API设计师,你不想让你的API消费者随便玩玩这样单调乏味的转换。 相反,您提供:

    public class IOUtils {
        public static void copy(InputStream input, OutputStream output);
        public static void copy(InputStream input, Writer output);
        public static void copy(InputStream input, Writer output, String encoding);
        public static void copy(InputStream input, Writer output, Charset encoding);
    }

这是一个很好的示例显示默认参数(可选的编码)以及参数类型选择(OutputStream vs. Writer or String vs. Charset 编码表示。


### 边注

我怀疑联合类型(union type)和默认参数船在java语言中很久以前的事了,而联合类型可能实现为语法糖,默认参数被暴力入到JVM,因为它将取决于JVM对命名参数的支持。

所显示的  [Ceylon language](http://blog.jooq.org/2013/12/03/top-10-ceylon-language-features-i-wish-we-had-in-java/) ,这两个功能覆盖方法重载所有用例的约99%,这就是为什么锡兰可以做完全没有重载——JVM之上!

重载是危险和非必须的

上面的例子表明,重载本质上只是一种帮助人类与一个API进行交互。 运行时,没有所谓的重载。 只有不同的、独特的方法签名的电话都与“静态”字节码(最近的操作码如invokedynamic)。 但关键是,电脑没有区别,如果上面的方法都调用 副本() ,或者如果他们被称为明确 m1() , 平方米() , m3() , m4() 。

另一方面,重载是真实的在Java源代码,编译器必须做很多工作找到最具体的方法,和其他应用JLS复杂的重载解析算法。 事态进一步恶化与每个新的Java语言版本。 举例来说,在Java 8方法引用将添加额外的痛苦API消费者,并从API设计师需要额外的照顾。 乔什•布洛赫考虑下面的例子:

    // Spot the bug static void pfc(List<Integer> x) { x.stream().map(Integer::toString).forEach( s -> System.out.println(换行打印)(s.charAt(0))); }

— Joshua Bloch (@joshbloch) July 20, 2015

你可以复制粘贴上面的代码到Eclipse来验证编译错误(注意,过时的编译器可能报告类型推断的副作用,而不是实际的错误)。 Eclipse编译错误报告的简化如下:

    static void pfc(List<Integer> x) {
        Stream<?> s = x.stream().map(Integer::toString);
    }

Ambiguous method reference: both toString() and 
toString(int) from the type Integer are eligible

上面的表达式是模棱两可的。 这意味着任何两个表达式如下:

    // Instance method:
    x.stream().map(i -> i.toString());
    
    // Static method:
    x.stream().map(i -> Integer.toString(i));

可以看到,歧义是立即解决了使用lambda表达式而不是方法的引用。 另外一种方法来解决这个问题模棱两可(向实例方法)将使用超类声明 toString() 相反,这不再是模棱两可的:

    // Instance method:
    x.stream().map(Object::toString);

### 结论

结论在这里API设计师是非常清楚的:

方法重载已成为一个更加危险的工具API设计师自Java 8

虽然上面的并不是“严重”,API消费者将浪费大量的时间克服这种认知摩擦时编译器拒绝看似正确的代码。 一个大的失礼,应对从这个例子:

从来没有混合相似实例和静态方法过载

事实上,这种放大静态方法时过载过载的名字 java . lang . object , 正如我们在之前的博客中解释道 。

有一个简单的原因上面的规则。 因为只有两个有效理由重载(默认参数和不兼容的参数选择),是毫无意义的提供一个静态过载的方法在同一个班。 暴露的一个更好的设计(JDK)是“伴侣类”——类似于Scala的同伴对象。 例如:

    // Instance logic
    public interface Collection<E> {}
    public class Object {}
    
    // Utilities
    public class Collections {}
    public final class Objects {}

通过改变名称空间方法,重载一直规避有点优雅,和前面的问题就不会出现了。

TL;博士:避免重载,除非添加值很便利