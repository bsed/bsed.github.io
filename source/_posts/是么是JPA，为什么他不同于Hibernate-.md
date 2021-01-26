---
title: "是么是JPA，为什么他不同于Hibernate?"
categories: [ "Java" ]
tags: [ "java","hibernate","jpa" ]
draft: false
slug: "is-it-jpa-why-he-is-different-from-hibernate"
date: "2015-09-17 21:38:00"
---

Java Persistence Architecture API(JPA)，即Java持久化体系结构API，它是一套规范，是用在Java的类/对象与数据库间读取、持久化和管理数据的一套标准。因为是一套规范，因此其并没有具体的实现细节，具体的实现交由JPA Provider负责。

<!--more-->

###JPA

让我们更进一步的看一下这个定义，正如其名字所意味的，JPA是一个规范，意味着他为开发符合特定标准的接口提供了指导原则。JPA值提供了接口，并未提供接口的具体实现，也就是说，其并没有操作对象和数据库的具体代码。

还需要关注的一个概念是对象关系映射（ORM），其经常被用于描述读取、持久化和管理Java对象和关系数据库一系列操作的过程。从另一个角度来想象JPA的概念，假如我们提供如下接口：

	public interface JPA {
		public void insert(Object obj);
		public void update(Object obj);
		public void delete(Object obj);
		public Object select();
	}

如果没有具体的实现，那么这个接口提供的立即值（immediate value）是什么？虽然这个接口有可能具备某个值，但缺乏实现的话其就不能提供任何值。如果在代码中使用这个接口的话，其不会被执行，因为接口中并没有定义实体对象，因此也就无法执行它。而JPA就是这样的一系列接口和注解组成的标准。

###JPA Provider

剩下的工作交由具体的JPA Provider去做，JPA Provider负责开发符合JPA规范的具体实现。Hibernate就是一个JPA Provider。只有使用了JPA的实现版本的类，Java对象才能被用于持久化关系数据库，因为其依赖于具体的实现代码来工作。

回到刚才的例子，那么Hibernate的代码应该是这个样子的：

	public class Hibernate implements JPA {
		public void insert(Object obj) {
		//Persistence code
		}
		public void update(Object obj) {
		//Persistence code
		}
		public void delete(Object obj) {
		//Persistence code
		}
		public Object select() {
		//Persistence code
		}
		public Object superSelect(){
		//Persistence Code
		}
	}

值得注意的是，除了实现JPA规范意外，Hibernate类还可以具备一些接口没有定义的方法，这个我们在后面还会讨论。给定JPA的实现，我们现在可以写代码用来将数据持久化到关系数据库中。

	public class MyApplication {
		public static JPA jpa = new Hibernate();
		
		public static void main(String[] args) {
			Object object = new Object();
			jpa.insert(object); //writes to DB
		}
	}

这段代码可以顺利的运行，但是过了一阵子你可能遇到性能问题，我们假设Hibernate在实现上存在的一些瑕疵导致了我们的性能问题。因为是例子的缘故，这里我们只是假设瑕疵的存在，并非Hibernate真的存在这些问题。

为了解决这个问题，假设存在另一个JPA实现能够解决这个问题。这个JPA Provider创建了他们自己的JPA实现版本，并能很好的解决性能不足的问题。假设代码如下

	public class ToThoughtJpa implements JPA {
		//Implementation
	}

因为在我们的应用中使用了JPA接口，因此，我们可以轻松的将代码转移到新的JPA实现上去（面向接口编程）。

	public class MyApplication {
		public static JPA jpa = new ToThoughtJpa();
		
		public static void main(String[] args) {
			Object object = new Object();
			jpa.insert(object); //writes to DB
		}
	}

上面的例子说明，如果我们选择使用JPA，那么我们可以将代码轻松的在不同版本的JPA Providers中切换，只要他们都符合JPA规范。但事实上，这并不是总是这么顺利，因为JPA定义的某些特性可能某些JPA Provider并没有实现，或者某些JPA Provider会增加一些自己的实现，例如上面的 superSelect() 方法。

###小结

JPA并不是一个具体实现，它不能为你的应用增加功能。它的目的是为众多JPA Provider提供实现用于ORM的标准规则。这使得你的应用能够简单的从一个JPA实现切换到另一个JPA实现。显然，Hibernate是JPA最流行的实现版本。因此，对于某些Hibernate独有的特性，你需要使用具体的Hibernate实现，因为JPA可能并不包括这些高级特性。

> 原文地址：http://blog-tothought.rhcloud.com//post/2