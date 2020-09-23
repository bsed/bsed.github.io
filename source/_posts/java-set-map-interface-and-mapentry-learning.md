---
title: Java集合Map接口与Map.Entry学习
date: 2011-08-12 09:19:00
updated: 2015-08-12 09:20:43
tags: 
- java
- java8
categories: 
- java

---
Map接口不是Collection接口的继承。Map接口用于维护键/值对(key/value pairs)。该接口描述了从不重复的键到值的映射。
　　(1) 添加、删除操作：
　　Object put(Object key, Object value): 将互相关联的一个关键字与一个值放入该映像。如果该关键字已经存在，那么与此关键字相关的新值将取代旧值。方法返回关键字的旧值，如果关键字原先并不存在，则返回null
　　Object remove(Object key): 从映像中删除与key相关的映射
　　void putAll(Map t): 将来自特定映像的所有元素添加给该映像
　　void clear(): 从映像中删除所有映射
　　“键和值都可以为null。但是，您不能把Map作为一个键或值添加给自身。”
　　(2) 查询操作：


<!--more-->


　　Object get(Object key): 获得与关键字key相关的值，并且返回与关键字key相关的对象，如果没有在该映像中找到该关键字，则返回null
　　boolean containsKey(Object key): 判断映像中是否存在关键字key
　　boolean containsValue(Object value): 判断映像中是否存在值value
　　int size(): 返回当前映像中映射的数量
　　boolean isEmpty() ：判断映像中是否有任何映射
　　(3) 视图操作 ：处理映像中键/值对组
　　Set keySet(): 返回映像中所有关键字的视图集
　　“因为映射中键的集合必须是唯一的，您用Set支持。你还可以从视图中删除元素，同时，关键字和它相关的值将从源映像中被删除，但是你不能添加任何元素。”
　　Collection values():返回映像中所有值的视图集
　　“因为映射中值的集合不是唯一的，您用Collection支持。你还可以从视图中删除元素，同时，值和它的关键字将从源映像中被删除，但是你不能添加任何元素。”
　　Set entrySet(): 返回Map.Entry对象的视图集，即映像中的关键字/值对
　　“因为映射是唯一的，您用Set支持。你还可以从视图中删除元素，同时，这些元素将从源映像中被删除，但是你不能添加任何元素。”
　 Map.Entry接口
　　Map的entrySet()方法返回一个实现Map.Entry接口的对象集合。集合中每个对象都是底层Map中一个特定的键/值对。
　　通过这个集合的迭代器，您可以获得每一个条目(唯一获取方式)的键或值并对值进行更改。当条目通过迭代器返回后，除非是迭代器自身的remove()方法或者迭代器返回的条目的setValue()方法，其余对源Map外部的修改都会导致此条目集变得无效，同时产生条目行为未定义。
　　(1) Object getKey(): 返回条目的关键字
　　(2) Object getValue(): 返回条目的值
　　(3) Object setValue(Object value): 将相关映像中的值改为value，并且返回旧值
例子：java代码
Map<String,Integer> map=new HashMap<String,Integer>();   
map.put("1", 1);   
map.put("2", 2);   
map.put("3", 3);   
map.put("3", 3);       
Iterator itor=map.entrySet().iterator();   
while(itor.hasNext()){   
  Map.Entry<String,Integer> entry=(Map.Entry<String,Integer>)itor.next();   
  System.out.println("key="+entry.getKey().toString());   
  System.out.println("values="+entry.getValue().toString());   
}   

 