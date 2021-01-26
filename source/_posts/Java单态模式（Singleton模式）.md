---
title: "Java单态模式（Singleton模式）"
categories: [ "Design Patterns" ]
tags: [ "javamodel","singleton" ]
draft: false
slug: "java-model-singleton-1"
date: "2014-10-03 13:23:00"
---

理解并使用设计模式，能够培养我们良好的面向对象编程习惯，同时在实际应用中，可以如鱼得水，享受游刃有余的乐趣。

Proxy是比较有用途的一种模式，而且变种较多，应用场合覆盖从小结构到整个系统的大结构，Proxy是代理的意思，我们也许有代理服务器等概念，代理概念可以解释为：在出发点到目的地之间有一道中间层，意为代理。

设计模式中定义：为其他对象提供一种代理以控制对这个对象的访问。
为什么要使用代理模式


<!--more-->


授权机制 不同级别的用户对同一对象拥有不同的访问权利，如Jive论坛系统中，就使用Proxy进行授权机制控制，访问论坛有两种人：注册用户和游客(未注册用户)，Jive中就通过类似ForumProxy这样的代理来控制这两种用户对论坛的访问权限。
某个客户端不能直接操作到某个对象，但又必须和那个对象有所互动。

举例两个具体情况：
如果那个对象是一个是很大的图片，需要花费很长时间才能显示出来，那么当这个图片包含在文档中时，使用编辑器或浏览器打开这个文档，打开文档必须很迅速，不能等待大图片处理完成，这时需要做个图片Proxy来代替真正的图片。
如果那个对象在Internet的某个远端服务器上，直接操作这个对象因为网络速度原因可能比较慢，那我们可以先用Proxy来代替那个对象。

总之原则是，对于开销很大的对象，只有在使用它时才创建，这个原则可以为我们节省很多宝贵的Java内存。所以，有些人认为Java耗费资源内存，我以为这和程序编制思路也有一定的关系。
如何使用代理模式

以Jive论坛系统为例，访问论坛系统的用户有多种类型：注册普通用户、论坛管理者、系统管理者、游客。注册普通用户才能发言，论坛管理者可以管理他被授权的论坛，系统管理者可以管理所有事务等，这些权限划分和管理是使用Proxy完成的。

Forum是Jive的核心接口，在Forum中陈列了有关论坛操作的主要行为，如论坛名称，论坛描述的获取和修改，帖子发表删除编辑等。

在ForumPermissions中定义了各种级别权限的用户：

    public class ForumPermissions implements Cacheable {
    /**
    * Permission to read object.
    */
    public static final int READ = 0;
    
    /**
    * Permission to administer the entire sytem.
    */
    public static final int SYSTEM_ADMIN = 1;
    
    /**
    * Permission to administer a particular forum.
    */
    public static final int FORUM_ADMIN = 2;
    
    /**
    * Permission to administer a particular user.
    */
    public static final int USER_ADMIN = 3;
    
    /**
    * Permission to administer a particular group.
    */
    public static final int GROUP_ADMIN = 4;
    
    /**
    * Permission to moderate threads.
    */
    public static final int MODERATE_THREADS = 5;
    
    /**
    * Permission to create a new thread.
    */
    public static final int CREATE_THREAD = 6;
    
    /**
    * Permission to create a new message.
    */
    public static final int CREATE_MESSAGE = 7;
    
    /**
    * Permission to moderate messages.
    */
    public static final int MODERATE_MESSAGES = 8;
    
    .....
    
    public boolean isSystemOrForumAdmin() {
    　return (values[FORUM_ADMIN] || values[SYSTEM_ADMIN]);
    }
    
    .....
    
    }

因此，Forum中各种操作权限是和ForumPermissions定义的用户级别有关系的，作为接口Forum的实现：ForumProxy正是将这种对应关系联系起来。比如，修改Forum的名称，只有论坛管理者或系统管理者可以修改，代码如下：

    public class ForumProxy implements Forum {
        private ForumPermissions permissions;
        private Forum forum;
        this.authorization = authorization;
    
        public ForumProxy(Forum forum, Authorization authorization,
        ForumPermissions permissions){
            this.forum = forum;
            this.authorization = authorization;
            this.permissions = permissions;
        }
        .....
        public void setName(String name) throws UnauthorizedException,
            ForumAlreadyExistsException{
    　　//只有是系统或论坛管理者才可以修改名称
    　　if (permissions.isSystemOrForumAdmin()) {
    　　　forum.setName(name);
    　　}
    　　else {
    　　　throw new UnauthorizedException();
    　　}
        }
        ...
    
    }

而DbForum才是接口Forum的真正实现，以修改论坛名称为例：

    public class DbForum implements Forum, Cacheable {
        ...
        public void setName(String name) throws ForumAlreadyExistsException {
    　　....
    　　this.name = name;
    　　//这里真正将新名称保存到数据库中
    　　saveToDb();
    　　....
        }
        ...
    }

凡是涉及到对论坛名称修改这一事件，其他程序都首先得和ForumProxy打交道，由ForumProxy决定是否有权限做某一样事情，ForumProxy是个名副其实的"网关"，"安全代理系统"。

在平时应用中，无可避免总要涉及到系统的授权或安全体系，不管你有无意识的使用Proxy，实际你已经在使用Proxy了。

我们继续结合Jive谈入深一点，下面要涉及到工厂模式了。

我们已经知道，使用Forum需要通过ForumProxy，Jive中创建一个Forum是使用Factory模式，有一个总的抽象类ForumFactory，在这个抽象类中，调用ForumFactory是通过getInstance()方法实现，这里使用了Singleton(也是设计模式之一)，getInstance()返回的是ForumFactoryProxy。

为什么不返回ForumFactory，而返回ForumFactory的实现ForumFactoryProxy？原因是明显的，需要通过代理确定是否有权限创建forum。

在ForumFactoryProxy中我们看到代码如下：

    public class ForumFactoryProxy extends ForumFactory {
    　protected ForumFactory factory;
    　protected Authorization authorization;
    　protected ForumPermissions permissions;
    
    　public ForumFactoryProxy(Authorization authorization, ForumFactory factory,ForumPermissions permissions){
    　　this.factory = factory;
    　　this.authorization = authorization;
    　　this.permissions = permissions;
    　}
    
    　public Forum createForum(String name, String description)
    　　throws UnauthorizedException, ForumAlreadyExistsException{
    　　//只有系统管理者才可以创建forum
    　　if (permissions.get(ForumPermissions.SYSTEM_ADMIN)) {
    　　　Forum newForum = factory.createForum(name, description);
    　　　return new ForumProxy(newForum, authorization, permissions);
    　　}
    　　else {
    　　　throw new UnauthorizedException();
    　　}
        }
    }

方法createForum返回的也是ForumProxy，Proxy就象一道墙，其他程序只能和Proxy交互操作。

注意到这里有两个Proxy：ForumProxy和ForumFactoryProxy。代表两个不同的职责：使用Forum和创建Forum。至于为什么将使用对象和创建对象分开，这也是为什么使用Factory模式的原因所在：是为了"封装" "分派"。换句话说，尽可能功能单一化，方便维护修改。

Jive论坛系统中其他如帖子的创建和使用，都是按照Forum这个思路而来的。

以上我们讨论了如何使用Proxy进行授权机制的访问，Proxy还可以对用户隐藏另外一种称为copy-on-write的优化方式。拷贝一个庞大而复杂的对象是一个开销很大的操作，如果拷贝过程中，没有对原来的对象有所修改，那么这样的拷贝开销就没有必要。用代理延迟这一拷贝过程。

比如：我们有一个很大的Collection，具体如hashtable，有很多客户端会并发同时访问它。其中一个特别的客户端要进行连续的数据获取，此时要求其他客户端不能再向hashtable中增加或删除 东东。

最直接的解决方案是：使用collection的lock，让这特别的客户端获得这个lock,进行连续的数据获取，然后再释放lock。

    public void foFetches(Hashtable ht){
    　synchronized(ht){
    　　//具体的连续数据获取动作..
    　}
    }

但是这一办法可能锁住Collection会很长时间，这段时间，其他客户端就不能访问该Collection了。

第二个解决方案是clone这个Collection，然后让连续的数据获取针对clone出来的那个Collection操作。这个方案前提是，这个Collection是可clone的，而且必须有提供深度clone的方法。Hashtable就提供了对自己的clone方法，但不是Key和value对象的clone。

    public void foFetches(Hashtable ht){
    　Hashttable newht=(Hashtable)ht.clone();
    }

问题又来了，由于是针对clone出来的对象操作，如果原来的母体被其他客户端操作修改了，那么对clone出来的对象操作就没有意义了。

最后解决方案：我们可以等其他客户端修改完成后再进行clone，也就是说，这个特别的客户端先通过调用一个叫clone的方法来进行一系列数据获取操作。但实际上没有真正的进行对象拷贝，直至有其他客户端修改了这个对象Collection。

使用Proxy实现这个方案，这就是copy-on-write操作。

Proxy应用范围很广，现在流行的分布计算方式RMI和Corba等都是Proxy模式的应用。
