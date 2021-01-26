---
title: "Jackson JsonNode.forEach() with Java 8 Consumer"
categories: [ "Java" ]
tags: [ "java","jackson","java8" ]
draft: false
slug: "jsonnodeforeach-with-java-jackson-8-consumer"
date: "2016-06-09 20:15:00"
---

Jackson提供了`jsonnode.foreach()`方法将接受java 8 consumer 定义遍历每个节点。consumer继承于超类jsonnode。它被定义为如下。`forEach(Consumer<? super JsonNode>  arg)` 我们可以定义consumer 继承 `JsonNode`。现在使用java 8 consumer迭代 解析JSON的简单示例，。首先查找输入文件。
*info.json*
```json 
{
  "id":1,
  "name":"yigrherb",
  "village":"Dadada",
  "district":"China",
  "state":"UP"
}  
```


<!--more-->


上面的json格式的文件将被解析

*IterateJSONwithConsumer.java*
```java
package com.concretepage;
import java.io.File;
import java.io.IOException;
import java.util.function.Consumer;
import org.codehaus.jackson.JsonFactory;
import org.codehaus.jackson.JsonNode;
import org.codehaus.jackson.JsonParseException;
import org.codehaus.jackson.JsonParser;
import org.codehaus.jackson.map.ObjectMapper;
public class IterateJSONwithConsumer {
	public static void main(String[] args) throws JsonParseException, IOException {
		JsonFactory jsonFactory = new JsonFactory();
		JsonParser jp = jsonFactory.createJsonParser(new File("D:/cp/info.json"));
		jp.setCodec(new ObjectMapper());
		JsonNode jsonNode = jp.readValueAsTree();
                Consumer<JsonNode> data = (JsonNode node) -> System.out.println(node.asText());
                jsonNode.forEach(data);
	}
} 
```

输入结果.
```
1
yigrherb
Dadada
China
UP 
```