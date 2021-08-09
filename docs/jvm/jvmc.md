

## 1.1 JVM常用启动配置


**查看当前gc**

> jps -lvm  </br>
> jmap -heap


**指定1M的最大堆内存和最小堆内存**

> java -Xmx1024M -Xms1024M  -XX:+PrintGCDetails GCLogAnalysis


**使用串行gc 并打印gc日志和堆dump  堆大小为521M**

> java -XX:+UseSerialGC -Xms512m -Xmx512m -Xloggc:gc.demo.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis

**使用cms并行gc**
> java -XX:+UseConcMarkSweepGC  -Xms512m -Xmx512m  -Xloggc:gc.demo.log  -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis

**使用G1 gc**
> java -XX:+UseG1GC -Xms512m -Xmx512m  --XX:+PrintGC  -XX:+PrintGCDateStamps GCLogAnalysis



## 1.2 测试工具

**1.sb压测工具**    

    sb -u 请求地址 -c 20 -N 60

如：sb -u http://119.28.63.204/wxmanage/getMessage -c 20 -N 60

windows常用  参数

-u 可用来指定要压测的网站或是API

-n 可用来指定要压的Request数量

-c 可用来指定Concurrent Request（并发请求）数量

-m 可用来指定要使用的HTTP Method

-h 可用来指定要显示HTTP Header

-q 可用来指定要显示Cookie

-N 指定运行的时间

**2.wk压测工具**





**3.arthas诊断工具**



## 1.3 主要知识点

### 什么是jvm

**请问JDK和JVM有什么区别**

**你认识哪些JVM厂商**

**oracleJDK与openjdk的区别**

**开发中使用什么版本jdk？生产环境？为什么这么设置**

### 什么是java字节码

**字节码文件中包含哪些内容**

**什么是常量**

**你怎么理解常量池**

### JVM运行时数据区有哪些

**什么是堆内存**

**堆内存包含哪些部分**

**什么是非堆内存**


### 什么是内存溢出

**什么是内存溢出**
**两者有什么关系**

### 给定一个具体的类，请分析对象的内存占用

**怎么计算**
**对象头包含什么内容**

### 常用的jvm参数有哪些

**设置堆内存XMX应该考虑哪些因素**

**假设物理内存8g，设置多大内存合适**

**-Xmx设置的值与jvm所占用的内存有什么关系**

**怎么样开启gc日志**

**g1启动helloworld**


### 垃圾回收期

**java8默认使用的垃圾回收器**

**java11默认使用的垃圾回收器**

**常见的垃圾回收期有哪些**

**什么是串行垃圾收集**

**并行垃圾收集**

**并发垃圾收集**

**增量式垃圾收集**

**什么是年轻代**

**什么是GC停顿**

**GC停顿与STW的关系**

### 线上问题排查

**如果CPU使用率飙升，怎么排查**

**系统相应变慢，怎么排查**

**系统性能一般怎么衡量**



****
****