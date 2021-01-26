---
title: "Java类成员的访问控制"
categories: [ "Java" ]
tags: [ "java" ]
draft: false
slug: "access-control-for-java-class-members"
date: "2010-09-27 20:56:00"
---

Java提供了4个访问控制符：private、protected、default和public，这4个访问控制符的访问权限由小到大private<protected<default<public。其中defualt是系统默认的访问控制级别，当不实用任何访问控制符来修饰类成员时，默认是default访问控制级别。这4个访问控制级别的详细介绍如下。
privaet（类内部访问权限）：使用private来修饰的类成员（包括Field、方法和构造器等）只能在当前类的内部被访问。
default（包访问权限）：类成员可以在类内部和相同包下的其他类访问。
protected（子类访问权限）：类成员可以在类内部、相同包下的类和继承子类访问。
public（公共访问权限）：最宽松的访问控制级别，可被所有外部类访问。
|  | private | default | protected | public |
| -- | -- | -- | -- | -- |
| 同一个类中 | 可访问 | 可访问 | 可访问 | 可访问 |
| 同一个包中 | | 可访问 | 可访问 | 可访问 |
| 子类中	 |  |   | 可访问 | 可访问 | 
| 全局范围 |   |   |  |可访问 |