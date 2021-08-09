# 一.Netty

##  1  Netty基础
   主要知识点：
>  基本概念介绍   </br>
>  常见使用案例 </br>
>  netty进阶   </br>


### 1.1 基本概念介绍

**Netty介绍**

网络应用开发框架

> 异步
 
> 事件驱动

> 基于NIO

适用于
> 服务端
> 客户端
> TCP/UDP


**Netty支持三种模式**

>1. Reactor单线程模型: Reactor单线程模型，指的是所有的I/O操作都在同一个NIO线程上面完成，NIO线程的职责如下：

- 作为NIO服务端，接收客户端的TCP连接；

- 作为NIO客户端，向服务端发起TCP连接；

- 读取通信对端的请求或者应答消息；

- 向通信对端发送消息请求或者应答消息；


>2. Reactor多线程模型: 一组NIO线程处理I/O操作


>3. 主从Reactor多线程模式 

- 特点是：服务端用于接收客户端连接的不再是1个单独的NIO线程，而是一个独立的NIO线程池。Acceptor接收到客户端TCP连接请求处理完成后（可能包含接入认证等），将新创建的SocketChannel注册到I/O线程池（sub reactor线程池）的某个I/O线程上，由它负责SocketChannel的读写和编解码工作。






**常见组件**

> Bootstrap: 启动线程，开启 socket

> EventLoopGroup

> EventLoop

> SocketChannel: 连接

> ChannelInitializer: 初始化

> ChannelPipeline: 处理器链

> ChannelHandler: 处理器


> channel

通道，Java NIO 中的基础概念,代表一个打开的连接,可执行读取/写入 IO 操作。
Netty 对 Channel 的所有 IO 操作都是非阻塞的。

1. option / handler / attr 方法

    option： 设置通道的选项参数， 对于服务端而言就是ServerSocketChannel， 客户端而言就是SocketChannel；

　    handler： 设置主通道的处理器， 对于服务端而言就是ServerSocketChannel，也就是用来处理Acceptor的操作；

　　　　　　对于客户端的SocketChannel，主要是用来处理 业务操作；

​         attr： 设置通道的属性；

option / handler / attr方法都定义在AbstractBootstrap中， 所以服务端和客户端的引导类方法调用都是调用的父类的对应方法。

​    2.childHandler / childOption / childAttr 方法（只有服务端ServerBootstrap才有child类型的方法）

　  对于服务端而言，有两种通道需要处理， 一种是ServerSocketChannel：用于处理用户连接的accept操作， 另一种是SocketChannel，表示对应客户端连接。而对于客户端，一般都只有一种channel，也就是SocketChannel。

　  因此以child开头的方法，都定义在ServerBootstrap中，表示处理或配置服务端接收到的对应客户端连接的SocketChannel通道。



> ChannelFuture

Java 的 Future 接口，只能查询操作的完成情况, 或者阻塞当前线程等待操作完成。Netty 封装一个 ChannelFuture 接口。我们可以将回调方法传给 ChannelFuture，在操作完成时自动执行


>Event & Handler

Netty 基于事件驱动，事件和处理器可以关联到入站和出站数据流


>Encoder & Decoder

处理网络 IO 时，需要进行序列化和反序列化, 转换 Java 对象与字节流。对入站数据进行解码, 基类是 ByteToMessageDecoder。对出站数据进行编码, 基类是 MessageToByteEncoder。


>ChannelPipeline

数据处理管道就是事件处理器链。有顺序、同一 Channel 的出站处理器和入站处理器在同一个列表中


>Event & Handler


![netty事件](https://user-images.githubusercontent.com/24818340/126331682-66b6e44c-1b2f-49d8-941e-c2e6ec080820.png)



>  bossgroup:   用于接收服务端的accept  ，即用于处理客户端的连接请求.不断监听是否有客户端的连接。当发现有一个新的客户端连接到来，bossgroup就会为此连接初始化所有资源，然后从所有workgroup池选出一个EventGroup绑定到此客户端连接中。接下来服务端和客户端的过程就在此EventLoop

> workergroup:   实际干活的类，负责客户端连接通道的IO操作.
>
> 他们自己关系（图片系转载）

![netty监听](https://user-images.githubusercontent.com/24818340/126867928-812e48fe-08ea-4af2-9d9f-0107233a2288.png)

>  服务端由两种线程池，用于Acceptor的React主线程和用于I/O操作的React从线程池； 客户端只有用于连接及IO操作的React的主线程池；
>
> erverBootstrap中定义了服务端React的"从线程池"对应的相关配置，都是以child开头的属性。 而用于"主线程池"channel的属性都定义在AbstractBootstrap中

### 1.2 常见使用案例



**常用参数**

ServerBootstrap  对象

属性： option  设置通道的选项参数， 对于服务端而言就是ServerSocketChannel， 客户端而言就是SocketChannel；

> ChannelOption.SO_BACKLOG
> // BACKLOG用于构造服务端套接字ServerSocket对象，标识当服务器请求处理线程全满时，用于临时存放已完成三次握手的请求的队列的最大长度。如果未设置或所设置的值小于1，Java将使用默认值50

属性： childOption

> ```
> ChannelOption.TCP_NODELAY
>      TCP_NODELAY就是用于启用或关于Nagle算法。如果要求高实时性，有数据发送时就马上发送，就将该选项设置为true关闭Nagle算法；如果要减少发送次数减少网络交互，就设置为false等累积一定大小后再发送。默认为false。
> ```

> ```
> ChannelOption.SO_KEEPALIVE
>  是否启用心跳保活机制。在双方TCP套接字建立连接后（即都进入ESTABLISHED状态）并且在两个小时左右上层没有任何数据传输的情况下，这套机制才会被激活。
> ```

> ```
> ChannelOption.SO_REUSEADDR
> SO_REUSEADDR允许启动一个监听服务器并捆绑其众所周知端口，即使以前建立的将此端口用做他们的本地端口的连接仍存在。这通常是重启监听服务器时出现，若不设置此选项，则bind时将出错。
> SO_REUSEADDR允许在同一端口上启动同一服务器的多个实例，只要每个实例捆绑一个不同的本地IP地址即可。对于TCP，我们根本不可能启动捆绑相同IP地址和相同端口号的多个服务器。
> SO_REUSEADDR允许单个进程捆绑同一端口到多个套接口上，只要每个捆绑指定不同的本地IP地址即可。这一般不用于TCP服务器。
> SO_REUSEADDR允许完全重复的捆绑：当一个IP地址和端口绑定到某个套接口上时，还允许此IP地址和端口捆绑到另一个套接口上。一般来说，这个特性仅在支持多播的系统上才有，而且只对UDP套接口而言（TCP不支持多播）
> ```

> ```
> ChannelOption.SO_RCVBUF
> 定义接收或者传输的系统缓冲区buf的大小，
> 
> ```

>```
>ChannelOption.SO_SNDBUF
>```

> ```
> EpollChannelOption.SO_REUSEPORT
> ```

> ```
> ChannelOption.SO_KEEPALIVE
> ```

> ```
> ChannelOption.ALLOCATOR
> Netty4使用对象池，重用缓冲区
> bootstrap.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
> bootstrap.childOption(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
> ```



*HttpRequest.Filter* 属性为我们提供了一个过滤和转换器，可以在进行页面处理之前对 *Http* 输入流数据进行更改



### 1.3 netty进阶



**Netty优化**

1、不要阻塞 EventLoop

2、系统参数优化
ulimit -a /proc/sys/net/ipv4/tcp_fin_timeout, TcpTimedWaitDelay

3、缓冲区优化
SO_RCVBUF/SO_SNDBUF/SO_BACKLOG/ REUSEXXX

4、心跳周期优化
心跳机制与短线重连

5、内存与 ByteBuffer 优化
DirectBuffer与HeapBuffer

6、其他优化
- ioRatio
- Watermark
- TrafficShaping