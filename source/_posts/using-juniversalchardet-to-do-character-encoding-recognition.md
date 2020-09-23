---
title: 使用juniversalchardet做字符编码识别
date: 2014-06-23 21:26:00
updated: 2016-03-08 21:34:18
tags: 
- golang
- ide
- vscode
categories: 
- go

---
在抓取网站的页面的时候最烦人的一件事情之一就是识别原站点的编码，通常来说只有GBK（GB2312）和UTF8两种，不过依旧需要读取大量Http头信息来识别，有些网站则由于历史原因两种编码同时存在，导致抓取中的一些问题。于是苦苦寻找，终于找到神器juniversalchardet。

首先引包
```xml
<!-- Mozilla的编码识别包 -->
<dependency>
    <groupId>com.googlecode.juniversalchardet</groupId>
    <artifactId>juniversalchardet</artifactId>
    <version>1.0.3</version>
</dependency>
```


<!--more-->


写个简单的Demo（我封装成工具方法了）

```java
import java.io.File;
import java.io.IOException;
import looly.github.hutool.FileUtil;
import org.mozilla.universalchardet.UniversalDetector;
/**
 * 编码识别工具类
 *
 */
public class CharsetDetectUtil {
    public static String detect(byte[] content) {
        UniversalDetector detector = new UniversalDetector(null);
        //开始给一部分数据，让学习一下啊，官方建议是1000个byte左右（当然这1000个byte你得包含中文之类的）
        detector.handleData(content, 0, content.length);
        //识别结束必须调用这个方法
        detector.dataEnd();
        //神奇的时刻就在这个方法了，返回字符集编码。
        return detector.getDetectedCharset();
    }
    public static void main(String[] args) throws IOException {
        byte[] bytes = FileUtil.readBytes(new File("E:/workspace/python/htmlUtil.py"));
        System.out.println(detect(bytes));
    }
}
```

**注意**

我这个工具方法还是有些可以改进的，官方建议重复利用UniversalDetector对象，可以设置为类属性，不过这时你就要调用detector.reset()方法重置UniversalDetector了（还有就是如果设置为类属性，编码识别这个方法就不是线程安全的了）。

官网上还提到了一个[jchardet](http://jchardet.sourceforge.net/)，是另一个字符编码识别库，只不过比[juniversalchardet](https://code.google.com/p/juniversalchardet/)老一些，速度也比不上后者，所以建议大家使用juniversalchardet。