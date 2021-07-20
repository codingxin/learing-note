# 一.Netty

##  1  Netty基础
   主要知识点：
>  基本概念介绍   </br>
>  常见使用案例 </br>
>  netty进阶   </br>


### 1.1 基本概念介绍

> channel

通道，Java NIO 中的基础概念,代表一个打开的连接,可执行读取/写入 IO 操作。
Netty 对 Channel 的所有 IO 操作都是非阻塞的。



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

>2123123


>2312

>2131






### 1.2 常见使用案例



### 1.3 netty进阶