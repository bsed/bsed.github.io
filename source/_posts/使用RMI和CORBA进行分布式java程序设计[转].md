---
title: "使用RMI和CORBA进行分布式java程序设计[转]"
categories: [ "Java" ]
tags: [ "java","rmi","corba" ]
draft: false
slug: "design-of-distributed-java-program-using-rmi-and-corba"
date: "2015-09-22 15:42:00"
---

    英文原文：http://developer.java.sun.com/developer/technicalArticles/RMI/rmi_
    corba/
这是2001 年的文章,到现在还是值得一读的.
  
RMI和corba是两种最重要和使用最广泛的分布式对象系统。 每种都有它的长处和短处。
这两种系统都在从电子商务到卫生保健等不同的行业成功的使用。在项目中使用这两种
分布机制中的任何一种都是一项很困难的任务。本文介绍了RMI和corba的机理和最主要
的是显示了如何开发一个有用的程序（一个从远程站点下载文件的程序）。于是有以下
## 内容：
  
一个分布式系统的简介
一个RMI和corba的简介
让你体验开发一个RMI和corba程序的滋味。
说明如何使用RMI和corba从远程机器交换文件
提供一个RMI和Corba的比较。
  


<!--more-->


## 客户/服务端模式
客户/服务模式是一个分布式计算应用。它通过使用一个应用程序(客户)和另一个程序(
服务端)交换数据。在这样的一个例子里面客户端和服务端一般使用同样的语言来编写,
使用相同的协议来相互通信。
在客户/服务模式应用到各种各样的地方的过程中，使用低层次的socket来开发是很典型
的。使用socket来开发客户/服务端模式意味着我们必须自己设计一种协议，该协议包含
客户端和服务端都统一的命令集 ,使得客户端和服务端能够通过这个协议来通信。例如
:HTTP协议提供了一个get的方法。所有的web服务器软件都集成了该功能，而所有的浏览
器软件都能够使用该功能来获得资料。
  
## 分布式对象模式
分布式对象系统是一个对象集合，通过定义很完善的统一的接口来分隔开的要求服务(客
户端)和功能服务(服务端)。换句话说客户端和公共服务的提供分隔开，这些服务包括数
据表现和执行的代码。这是一个分辨分布式对象模式和客户/服务模式的主要不同。
在分布式对象模式里,客户端发送一个消息到一个对象，由这个对象解释这个消息然后决
定应该由什么服务来完成。这个服务,方法或选择的完成可能是被一个对象或是被一个b
roker。RMI和corba就是这种模式的例子。
## RMI
RMI是一个分布式对象模式。它使得使用java开发分布式程序更加容易。由于不需要设计
协议(这基本是一个错误的任务) 使得使用RMI开发分布式程序比使用socket更加容易。
在RMI里面设计者就象在调用一个本地的类的方法一样,而实际上是在调用的时候相应的
参数被发送到远端的对象和然后被解释。最后结果返回给调用者。
  
### 一个 RMI应用的流程
使用 RMI开发一个分布式应用包括如下几个步骤

 1. 1)定义一个远端的接口
 2. 2)实现这个远端的接口
 3. 3)开发一个服务端
 4. 4)开发一个客户端
 5. 5)生成Stubs 和Skeletons,运行RMI注册器,服务端 和客户端

我们现在通过开发一个文件交换程序来解释这些步骤
#### 例子:文件交换程序
这个应用允许客户端从服务端交换(或下载)所有类型的文件。第一步是定义一个远程的
接口，这个接口指定了的签名方法将被服务端提供和被客户端调用。
#### 定义一个远程接口
这个程序的远程接口在代码例子1中列出，接口FileInterface提供一个downloadFile这
个带一个字符窜(文件名)变量的的方法,然后返回以一个字符窜序列的形式的相应文件数
据。
代码例子1: FileInterface.java

    import java.rmi.Remote;
    import java.rmi.RemoteException;
    public interface FileInterface extends Remote {
        public byte[] downloadFile(String fileName) throws
        RemoteException;
    }

注意接口FileInterface的如下特征:
它必须定义成public,这是为了让客户端能够通过调用远程接口来间接调用远程的对象。
  
必须使用从Remote接口扩展过来,这是创建一个远程的对象的需要。
每个接口方法中必须抛出java.rmi.RemoteException错误。
  
## 实现远程的接口
下一步是实现远程的接口FileInterface。实现的例子在代码例子 2中列出。类FileImp
l从UnicastRemoteObject扩展来。这显示出FileImpl类是用来创建一个单独的，不能复
制的，远程的对象，
这个对象使用RMI 的默认的基于TCP的通信方式。
代码例子 2: FileImpl.java

    import java.io.*;
    import java.rmi.*;
    import java.rmi.server.UnicastRemoteObject;
    public class FileImpl extends UnicastRemoteObject
       implements FileInterface {
        private String name;
        public FileImpl(String s) throws RemoteException{
           super();
           name = s;
        }
        public byte[] downloadFile(String fileName){
           try {
              File file = new File(fileName);
              byte buffer[] = new byte[(int)file.length()];
              BufferedInputStream input = new
           BufferedInputStream(new FileInputStream(fileName));
              input.read(buffer,0,buffer.length);
              input.close();
              return(buffer);
           } catch(Exception e){
              System.out.println("FileImpl: "+e.getMessage());
              e.printStackTrace();
              return(null);
           }
        }
    }

  
#### 编写服务端
第3步是实现一个服务端。有3件事服务端需要去做:
1)创建一个RMISecurityManager实例,然后安装它。
2)创建一个远程对象的实例(这个例子中是FileImpl )
3)使用RMI注册工具来注册这个对象。
代码例子 3中显示了如何操作的。
代码例子 3: FileServer.java

    import java.io.*;
    import java.rmi.*;
    public class FileServer {
        public static void main(String argv[]) {
           if(System.getSecurityManager() == null) {
              System.setSecurityManager(new RMISecurityManager());
           }
           try {
              FileInterface fi = new FileImpl("FileServer");
              Naming.rebind("//127.0.0.1/FileServer", fi);
           } catch(Exception e) {
              System.out.println("FileServer: "+e.getMessage());
              e.printStackTrace();
           }
        }
    }

声明Naming.rebind("//127.0.0.1/FileServer", fi) 中假定了RMI注册工具(RMI regi
stry )使用1099端口并在运行中。如果你在其他的端口运行了RMI注册工具，你必须在这
个声明中定义。例如如果RMI注册工具在4500端口运行。你的声明要变成
Naming.rebind("//127.0.0.1:4500/FileServer", fi)
另外我们已经同时假定了我们的服务端和RMI注册工具是运行在同一台机器上的。如果不
是的话你要修改rebind方法中的地址。
编写客户端 下一步是编写一个客户端，客户端可以远程调用远程接口(FileInterface)
中说明的任何一个方法。无论如何实现，客户端必须先从RMI注册工具获得一个远程对象
的引用。当引用获得后方法downloadFile 被调用。客户端的例子在代码例子4中,执行过
程中客户端从命令行中获得两个参数，第一个是要下载的文件名,第二个是要下载的机器
的地址。对应地址的机器上运行服务端。
代码例子 4: FileClient.java

    import java.io.*;
    import java.rmi.*;
    public class FileClient{
        public static void main(String argv[]) {
           if(argv.length != 2) {
             System.out.println("Usage: java FileClient fileName machineName");
             System.exit(0);
           }
           try {
              String name = "//" + argv[1] + "/FileServer";
              FileInterface fi = (FileInterface) Naming.lookup(name);
              byte[] filedata = fi.downloadFile(argv[0]);
              File file = new File(argv[0]);
              BufferedOutputStream output = new
                BufferedOutputStream(new FileOutputStream(file.getName()));
              output.write(filedata,0,filedata.length);
              output.flush();
              output.close();
           } catch(Exception e) {
              System.err.println("FileServer exception: "+ e.getMessage());
              e.printStackTrace();
           }
        }
    }

运行程序
为了运行程序我们必须生成stubs 和 skeletons,为了生成stubs 和 skeletons，我们使
用rmic来编译:
prompt> rmic FileImpl
将会生成两个文件FileImpl_Stub.class和 FileImpl_Skel.class. stub是客户端的代理
而skeleton是服务端的框架。
下一步是编译服务端和客户端。使用javac来编译。注意如果服务端和客户端在两个不同
的机器，为了编译客户端你必须复制一个FileInterface接口。
最后,到你运行RMI注册工具和运行服务端和客户端的时候了。使用rmiregistry 或者 s
tart rmiregistry 命令来运行RMI注册工具到window系统的默认的端口上,要运行RMI注
册工具在一个其他的端口的话使用端口参数。
prompt> rmiregistry portNumber
RMI注册工具运行之后,你要运行服务FileServer，因为RMI的安全机制将在服务端发生作
用,所以你必须增加一条安全策略。以下是对应安全策略的例子
grant {
permission java.security.AllPermission "", "";
};
注意:这是一条最简单的安全策略,它允许任何人做任何事,对于你的更加关键性的应用,
你必须指定更加详细安全策略。
现在为了运行服务端，你需要除客户类(FileClient.class)之外的所有的类文件。确认
安全策略在policy.txt文件之后,使用如下命令来运行服务器。
prompt> java -Djava.security.policy=policy.txt FileServer
为了在其他的机器运行客户端程序你需要一个远程接口(FileInterface.class) 和一个
stub(FileImpl_Stub.class)。 使用如下命令运行客户端
prompt> java FileClient fileName machineName
这里fileName是要下载的文件名,machineName 是要下载的文件所在的机器(也是服务端
所在的机器)
如果所有都可以了话,当客户端运行后，这个文件将下载到本地。
----------------------------------------------------------------------------
----
我们提到如果要运行客户端,我们需要远程接口和stub,另外一个更好的方法是使用RMI动
态类加载，这个方法使得你不必要复制远程接口和stub。取而代之的是,它们能够在一个
共同的目录里面被客户端和服务端查找到。为了做到这个你必须使用以下命令来运行客
户端。
使用如下命令
java -Djava.rmi.server.codebase=http://hostname/locationOfClasses FileClient
  fileName machineName.
更多的信息看这里Dynamic Code Loading using RMI.
----------------------------------------------------------------------------
----
## CORBA
CORBA(The Common Object Request Broker Architecture:通用对象请求代理结构)是对
象管理组织(ORG)在分布式对象项目方面资助的一个工业标准。CORBA只是一个标准，一
个CORBA服务以ORB(Object Request Broker对象要求代理)的形式来运行，市场上有很多
可用的CORBA ORB服务软件例如VisiBroker, ORBIX, 和其他一些ORB软件。JavaIDL 是其
中的一个，它是jdk1.3或jdk1.3以上版本的一个核心开发包。
CORBA的设计是独立于平台和语言的，因此CORBA可以在任何平台上运行，可以定位在网
络的任何地方，能够使用任何有IDL（Interface Definition Language )映射的语言。
  
和RMI相类似,CORBA对象使用接口来描述,然而接口在CORBA中是定义在IDL中的。IDL很类
似于C++,但是IDL不是编程语言，更多的关于CORBA的介绍在这里有Distributed Progra
mming with Java: Chapter 11 (Overview of CORBA).
CROBA程序的编写过程
开发CORBA有很多复杂的步骤,如下
1.定义一个IDL
2.把IDL接口映射到java
3.开发server端
4.开发client端
5.运行名字服务,服务端 和客户端
我们现在一步步的解释一个基corba的文件交换程序的开发,这有点类似于我们上面讲的
RMI程序的开发,我们这里使用JavaIDL(一个jdk1.3的核心开发包).
定义接口
当你定义一个corba的接口时,考虑一下服务要支持的操作，在这个程序里面客户端将包
含一个下载文件的方法。代码例子5显示了一个FileInterface接口，Data是一个用type
def关键字引入的新的类型描述。sequence 在IDL中除了不能定义固定大小外其他都类似
于数组，octet是一个8字节的量，相当于java中的byte。downloadFile 方法中的参数是
一个string类型并被定义成in 类型。IDL定义了3中传输模式:in(用来接收客户端到服务
端的输入),out(用来接收服务端到客户端的输出)和inout(同时用来输入和输出)。
代码例子 5: FileInterface.idl

    interface FileInterface {
        typedef sequence<octet> Data;
        Data downloadFile(in string fileName);
    };

一旦你定义了一个IDL接口,你就要开始编译它。JDK1.3+包含了一个idlj 编译器，它可
以用来映射IDL到java的声明。
idlj 可以通过不同的命令产生不同的输出如客户端的stubs，服务端的skeletons。
-f<side> 参数用来指定产生什么。side是如下的client, server, 或 all 参数分别用
来表示客户端的stubs 和服务端的 skeletons。在这个例子里由于服务端和客户端在两
个不同的机器上，所以我们在客户端上使用-fclient 在服务端上使用-fserver 。
现在让我们编译FileInterface.idl 来产生服务端的skeletons，使用如下命令
prompt> idlj -fserver FileInterface.idl
执行接口
现在我们提供一个对downloadFile方法的执行。这个执行就像一个仆人，你可以从例子
6中看出 FileServant 类从_FileInterfaceImplBase 类扩展过来。从这个类可以看出这
个仆人是一个corba对象
例子6代码: FileServant.java

    import java.io.*;
    public class FileServant extends _FileInterfaceImplBase {
        public byte[] downloadFile(String fileName){
           File file = new File(fileName);
           byte buffer[] = new byte[(int)file.length()];
           try {
              BufferedInputStream input = new
                BufferedInputStream(new FileInputStream(fileName));
              input.read(buffer,0,buffer.length);
              input.close();
           } catch(Exception e) {
              System.out.println("FileServant Error: "+e.getMessage());
              e.printStackTrace();
           }
           return(buffer);
        }
    }

开发服务端
下一步是编写corba服务端。在例子7中实现了一个corba服务端的类 FileServer。它通
过以下的步骤来实现。
1)定义一个orb
2)创建一个FileServant对象
3)登记到corba命名服务中(COS Naming)
4)打印状态消息
5)等待客户端请求
例子7代码: FileServer.java

    import java.io.*;
    import org.omg.CosNaming.*;
    import org.omg.CosNaming.NamingContextPackage.*;
    import org.omg.CORBA.*;
    public class FileServer {
        public static void main(String args[]) {
           try{
              // create and initialize the ORB
              ORB orb = ORB.init(args, null);
              // create the servant and register it with the ORB
              FileServant fileRef = new FileServant();
              orb.connect(fileRef);
              // get the root naming context
              org.omg.CORBA.Object objRef =
                 orb.resolve_initial_references("NameService");
              NamingContext ncRef = NamingContextHelper.narrow(objRef);
              // Bind the object reference in naming
              NameComponent nc = new NameComponent("FileTransfer", " ");
              NameComponent path[] = {nc};
              ncRef.rebind(path, fileRef);
              System.out.println("Server started....");
              // Wait for invocations from clients
              java.lang.Object sync = new java.lang.Object();
              synchronized(sync){
                 sync.wait();
              }
           } catch(Exception e) {
              System.err.println("ERROR: " + e.getMessage());
              e.printStackTrace(System.out);
           }
        }
    }

一旦FileServer 获得一个orb, 它能够登记corba服务。它使用由omg建议的由Java IDL
  实现的corba名字服务(COS Naming Service )来登记。它是目录服务中从根节点开始的
一个目录节点。这是一个普通的corba对象。可以当成NamingContext对象来使用。它必
须能够被narrowed down (换句话说是分级(casted))
到相应的类型。这是使用一个声明来实现的。
NamingContext ncRef = NamingContextHelper.narrow(objRef);
ncRef对象现在是org.omg.CosNaming.NamingContext对象。你可以使用它来登记一个co
rba服务。 rebind 调用方法可以实现。
开发客户端
下一步是开发一个客户端，在例子8里面实现了，一旦得到了一个到名字服务的引用，它
就能够被用来进入名字服务和能够用来查找一些服务(例如这里查找的是FileTransfer  
服务)当FileTransfer 服务被找到的时候，downloadFile 方法将被调用。
例子8代码:FileClient

    import java.io.*;
    import java.util.*;
    import org.omg.CosNaming.*;
    import org.omg.CORBA.*;
    public class FileClient {
        public static void main(String argv[]) {
           try {
              // create and initialize the ORB
              ORB orb = ORB.init(argv, null);
              // get the root naming context
              org.omg.CORBA.Object objRef =
                 orb.resolve_initial_references("NameService");
              NamingContext ncRef = NamingContextHelper.narrow(objRef);
              NameComponent nc = new NameComponent("FileTransfer", " ");
              // Resolve the object reference in naming
              NameComponent path[] = {nc};
              FileInterfaceOperations fileRef =
                 FileInterfaceHelper.narrow(ncRef.resolve(path));
              if(argv.length < 1) {
                 System.out.println("Usage: java FileClient filename");
              }
              // save the file
              File file = new File(argv[0]);
              byte data[] = fileRef.downloadFile(argv[0]);
              BufferedOutputStream output = new
                BufferedOutputStream(new FileOutputStream(argv[0]));
              output.write(data, 0, data.length);
              output.flush();
              output.close();
           } catch(Exception e) {
              System.out.println("FileClient Error: " + e.getMessage());
              e.printStackTrace();
           }
        }
    }

运行应用
最后一步是运行这个应用(万里长征最后一步 哈哈高兴吧)。这里有几个小的步骤。
1）运行CORBA名字服务。这里可以使用tnameserv命令。它默认运行在900端口。你还可
以改变端口号
例如使用如下命令让你的服务运行在2500端口。
prompt> tnameserv -ORBinitialPort 2500
2)运行服务端
如果CORBA名字服务在默认的端口的时候使用如下命令。
prompt> java FileServer
如果你的CORBA名字服务在自己定义的端口如2500,使用如下命令，这里使用-ORBInitia
lPort 来定义端口
prompt> java FileServer -ORBInitialPort 2500
3)创建客户端的Stubs。我们在运行客户端之前我们必须创建一个Stubs。首先我们要得
到一个FileInterface.idl 的复制，然后用如下命令编译。
prompt> idlj -fclient FileInterface.idl
4)运行客户端。现在你可以运行客户端了，以下命令是假定corba名字服务在2500端口处
监听。
prompt> java FileClient hello.txt -ORBInitialPort 2500
这样hello.txt文件可以从服务端下载下来了。
另外的，有些选项通过代码定义的属性来定义。比如代替初始化orb的代码如下
ORB orb = ORB.init(argv, null);
它能修改一些参数，使得orb在2500端口提供和corba服务的名字叫gosling。例子如下。
  

    Properties props = new Properties();
    props.put("org.omg.CORBA.ORBInitialHost", "gosling");
    props.put("orb.omg.CORBA.ORBInitialPort", "2500");
    ORB orb = ORB.init(args, props);

练习
在这个文件交换程序里面，客户端（不管是corba 还是rmi）需要知道将要下载的文件名
,可是没有方法提供列出可以用到的文件。作为一个练习，你可能想加入一些方法来比如
提供列出可以用到的文件名来提高这个应用程序的可使用性。另外，你可能想开发一个
基于图形的客户端来代替字符客户端。当客户端运行的时候它调用一个在服务端的方法
来得到文件列表然后弹出一个菜单来显示所有用户可以选择下载的文件。用户可以选择
一个或多个文件来下载. 如下 图1
Figure 1: GUI-based File Transfer Client
## CORBA vs. RMI
从编码来看,很明显,RMI对于java 开发人员来说很容易使用。他们不需要了解IDL语言  
Interface Definition Language 。
然而一般来说corba和RMI在如下方面各不相同。
(1)corba的interfaces(接口)使用IDL来定义，RMI 接口使用java来定义。RMI-IIOP允许
所有的interfaces(接口)使用java来编写。
(2)COrba提供in 和 out参数。同时RMI不提供，这是因为本地对象能够复制。
(3)CORBA使用一种特殊的语言来设计interfaces(接口),这意味着一些对象可以使用jav
a来编写 而另外一些类可以使用 另外的语言例如C++来编写它们能够协同的工作。因此
corba是一种连接各种孤立语言的理想机制。而RMI只是对所有java编写的对象设计的。
但是RMI-IIOP提供协同工作的能力。
(4)CORBA对象不被自动回收.就像我们以上提到的,corba是一种独立的特殊语言，另外一
些语言(例如C++)不提供垃圾收集。这可能会造成不利的情况，因为corba对象一旦创建
，它将一直存在直到你杀掉它，而决定一个对象什么时候将被杀掉不是一件很简单的工
作。而RMI对象能够自动的被收集。
结论
可以使用RMI和JavaIDL（一个corba的实现）开发一个基于分布式对象的java应用程序。
使用这两种技术从第一步到定义一个对象的接口都是很相似的。
然而和RMI把对象接口（interfaces）定义在java中不同的是，Corba的对象接口(inter
faces)定义在IDL(Interface Definition Language )中,这样增加了其他层的复杂性，
使得需要开发人员需要了解IDL和IDL到java 的映射。
在这两种机制之间做选择依靠手头的项目和项目的要求。我希望本文能够给你提供足够
的信息来开始开发一个基于对象分布式应用程序和给你提供足够的指导
来选择一个分布机制。
更多的相关参考:
- RMI
- CORBA Specification (OMG)
- JavaIDL
- Distributed Programming with Java book (Chapter 11: Overview of CORBA)
- CORBA Server and Servlet Client
- RMI-IIOP
关于作者
Qusay H. Mahmoud 专门提供关于 Java 的咨询和培训。他发表了许多关于java的文章  
, 它还是
<<Distributed Programming with Java >> (1999)和 <<Learning Wireless Java>>(2
000)的作者。
  
-- 
：到底爱是什么？
  
   爱就是为了心上人无条件付出，牺牲，一心只想要她得到幸福，快乐
  
：错！爱是霸占，摧毁，还有破坏，为了得到对方不择手段，不惜让对方
   伤心，必要的时候一拍两散，玉石俱焚 
来源:[http://www.newsmth.net/nForum/#!article/DOC/5452](http://www.newsmth.net/nForum/#!article/DOC/5452)