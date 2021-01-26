---
title: " JAVA NIO 实例 [转]"
categories: [ "Java" ]
tags: [ "java","nio" ]
draft: false
slug: "nio-java-instance"
date: "2012-09-21 12:39:00"
---

> 来源于互联网　地址不详

我们都知道TCP是面向连接的传输层协议，一个socket必定会有绑定一个连接，在普通的BIO(阻塞式IO)中，需要有三次握手，然后一般的socket编程就是这样的形式。

Socket服务器端流程如下：加载套接字->创建监听的套接字->绑定套接字->监听套接字->处理客户端相关请求。

Socket客户端同样需要先加载套接字，然后创建套接字，不过之后不用绑定和监听了，而是直接连接服务器，发送相关请求。

他们一直就占用这个连接，如果有信息发送，那么就响应，否则就一直阻塞着。如果有多连接，那么就要使用多线程，一个线程处理一个连接，在连接还少的情况下，是允许的，但如果同时处理的连接过多比如说1000，那么在win平台上就会遇到瓶颈了如果2000，那么在linux上就遇到瓶颈了，因为在不同的平台上每一个进程能够创建的线程数是有限度的，并且过多的线程必将会引起系统对线程调度的效率问题，再怎么也要保证线程优先队列，阻塞队列；假设一千个线程，一个线程最少一兆的栈大小，对内存也是一个很大的消耗。
总之阻塞式的IO是：一连接<一一一>一线程 。


<!--more-->


然后出现了NIO，在java1.4引入了java.nio包，java new I/O。引入了操作系统中常用的缓冲区和通道等概念。

**缓冲区**： 在操作系统中缓冲区是为了解决CPU的计算速度和外设输入输出速度不匹配的问题，因为外设太慢了，如果没有缓冲区，那么CPU在外设输入的时候就要一直等着，就会造成CPU处理效率的低下，引入了缓冲之后，外设直接把数据放到缓冲中，当数据传输完成之后，给CPU一个中断信号，通知CPU：“我的数据传完了，你自己从缓冲里面去取吧”。如果是输出也是一样的道理。

**通道**： 那么通道用来做什么呢？其实从他的名字就可以看出，它就是一条通道，您想传递出去的数据被放置在缓冲区中，然后缓冲区中怎么从哪里传输出去呢？或者外设怎么把数据传输到缓冲中呢？这里就要用到通道。它可以进一步的减少CPU的干预，同时更有效率的提高整个系统的资源利用率，例如当CPU要完成一组相关的读操作时，只需要向I/O通道发送一条指令，以给出其要执行的通道程序的首地址和要访问的设备，通道执行通道程序便可以完成CPU指定的I/O任务。

**选择器**： 另外一项创新是选择器，当我们使用通道的时候也许通道没有准备好，或者有了新的请求过来，或者线程遇到了阻塞，而选择器恰恰可以帮助CPU了解到这些信息，但前提是将这个通道注册到了这个选择器。

*Server:*

```
    package cn.vicky.channel;  
      
    import java.io.IOException;  
    import java.net.InetSocketAddress;  
    import java.nio.ByteBuffer;  
    import java.nio.channels.SelectionKey;  
    import java.nio.channels.Selector;  
    import java.nio.channels.ServerSocketChannel;  
    import java.nio.channels.SocketChannel;  
    import java.nio.channels.spi.SelectorProvider;  
    import java.util.Iterator;  
      
    /**    
     * TCP/IP的NIO非阻塞方式   
     * 服务器端   
     * */  
    public class Server implements Runnable {  
      
        //第一个端口     
        private Integer port1 = 8099;  
        //第二个端口     
        private Integer port2 = 9099;  
        //第一个服务器通道 服务A     
        private ServerSocketChannel serversocket1;  
        //第二个服务器通道 服务B     
        private ServerSocketChannel serversocket2;  
        //连接1     
        private SocketChannel clientchannel1;  
        //连接2     
        private SocketChannel clientchannel2;  
      
        //选择器，主要用来监控各个通道的事件     
        private Selector selector;  
          
        //缓冲区     
        private ByteBuffer buf = ByteBuffer.allocate(512);  
          
        public Server() {  
            init();  
        }  
      
        /**   
         * 这个method的作用 
         * 1：是初始化选择器   
         * 2：打开两个通道   
         * 3：给通道上绑定一个socket   
         * 4：将选择器注册到通道上   
         * */  
        public void init() {  
            try {  
                //创建选择器     
                this.selector = SelectorProvider.provider().openSelector();  
                //打开第一个服务器通道     
                this.serversocket1 = ServerSocketChannel.open();  
                //告诉程序现在不是阻塞方式的     
                this.serversocket1.configureBlocking(false);  
                //获取现在与该通道关联的套接字     
                this.serversocket1.socket().bind(new InetSocketAddress("localhost", this.port1));  
                //将选择器注册到通道上，返回一个选择键     
                //OP_ACCEPT用于套接字接受操作的操作集位     
                this.serversocket1.register(this.selector, SelectionKey.OP_ACCEPT);  
      
                //然后初始化第二个服务端     
                this.serversocket2 = ServerSocketChannel.open();  
                this.serversocket2.configureBlocking(false);  
                this.serversocket2.socket().bind(new InetSocketAddress("localhost", this.port2));  
                this.serversocket2.register(this.selector, SelectionKey.OP_ACCEPT);  
      
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
      
        }  
      
        /**   
         * 这个方法是连接   
         * 客户端连接服务器   
         * @throws IOException    
         * */  
        public void accept(SelectionKey key) throws IOException {  
            ServerSocketChannel server = (ServerSocketChannel) key.channel();  
            if (server.equals(serversocket1)) {  
                clientchannel1 = server.accept();  
                clientchannel1.configureBlocking(false);  
                //OP_READ用于读取操作的操作集位     
                clientchannel1.register(this.selector, SelectionKey.OP_READ);  
            } else {  
                clientchannel2 = server.accept();  
                clientchannel2.configureBlocking(false);  
                //OP_READ用于读取操作的操作集位     
                clientchannel2.register(this.selector, SelectionKey.OP_READ);  
            }  
        }  
      
        /**   
         * 从通道中读取数据   
         * 并且判断是给那个服务通道的   
         * @throws IOException    
         * */  
        public void read(SelectionKey key) throws IOException {  
      
            this.buf.clear();  
            //通过选择键来找到之前注册的通道     
            //但是这里注册的是ServerSocketChannel为什么会返回一个SocketChannel？？     
            SocketChannel channel = (SocketChannel) key.channel();  
            //从通道里面读取数据到缓冲区并返回读取字节数     
            int count = channel.read(this.buf);  
      
            if (count == -1) {  
                //取消这个通道的注册     
                key.channel().close();  
                key.cancel();  
                return;  
            }  
      
            //将数据从缓冲区中拿出来     
            String input = new String(this.buf.array()).trim();  
            //那么现在判断是连接的那种服务     
            if (channel.equals(this.clientchannel1)) {  
                System.out.println("欢迎您使用服务A");  
                System.out.println("您的输入为：" + input);  
            } else {  
                System.out.println("欢迎您使用服务B");  
                System.out.println("您的输入为：" + input);  
            }  
      
        }  
      
        @Override  
        public void run() {  
            while (true) {  
                try {  
                    System.out.println("running ... ");  
                    //选择一组键，其相应的通道已为 I/O 操作准备就绪。     
                    this.selector.select();  
      
                    //返回此选择器的已选择键集     
                    //public abstract Set<SelectionKey> selectedKeys()     
                    Iterator selectorKeys = this.selector.selectedKeys().iterator();  
                    while (selectorKeys.hasNext()) {  
                        System.out.println("running2 ... ");  
                        //这里找到当前的选择键     
                        SelectionKey key = (SelectionKey) selectorKeys.next();  
                        //然后将它从返回键队列中删除     
                        selectorKeys.remove();  
                        if (!key.isValid()) { // 选择键无效  
                            continue;  
                        }  
                        if (key.isAcceptable()) {  
                            //如果遇到请求那么就响应     
                            this.accept(key);  
                        } else if (key.isReadable()) {  
                            //读取客户端的数据     
                            this.read(key);  
                        }  
                    }  
                } catch (Exception e) {  
                    e.printStackTrace();  
                }  
            }  
        }  
      
        public static void main(String[] args) {  
            Server server = new Server();  
            Thread thread = new Thread(server);  
            thread.start();  
        }  
    }  
```

*Client:*

```
    package cn.vicky.channel;  
      
    import java.io.IOException;  
    import java.net.InetSocketAddress;  
    import java.nio.ByteBuffer;  
    import java.nio.channels.SocketChannel;  
    import java.net.InetAddress;  
      
    /**   
     * TCP/IP的NIO非阻塞方式   
     * 客户端   
     * */  
    public class Client {  
      
        //创建缓冲区     
        private ByteBuffer buffer = ByteBuffer.allocate(512);  
        //访问服务器     
      
        public void query(String host, int port) throws IOException {  
            InetSocketAddress address = new InetSocketAddress(InetAddress.getByName(host), port);  
            SocketChannel socket = null;  
            byte[] bytes = new byte[512];  
            while (true) {  
                try {  
                    System.in.read(bytes);  
                    socket = SocketChannel.open();  
                    socket.connect(address);  
                    buffer.clear();  
                    buffer.put(bytes);  
                    buffer.flip();  
                    socket.write(buffer);  
                    buffer.clear();  
                } catch (Exception e) {  
                    e.printStackTrace();  
                } finally {  
                    if (socket != null) {  
                        socket.close();  
                    }  
                }  
            }  
        }  
      
        public static void main(String[] args) throws IOException {  
            new Client().query("localhost", 8099);  
      
        }  
    }  
```
以上的服务端一个线程监听两个服务，整个服务端只有一个阻塞的方法：

    //选择一组键，其相应的通道已为 I/O 操作准备就绪。  
    this.selector.select();  

当客户请求服务器的时候，那么这造成了TCP没有面向连接的假象，其实至少在传输数据的时候是连接的，只是在一次I/O请求结束之后服务器端就把连接给断开，继而继续去处理更多的请求。而在客户端，可以看到也是遇到一次请求的时候就connect服务端一次。所以TCP还是面向连接的。

     现在终于知道了为什么叫非阻塞式IO了，大概就是这个意思。