# 1.JVM

>  jvm内存模型  </br>
>  jvm启动参数  </br>

![jvm](https://user-images.githubusercontent.com/24818340/124374460-f4e53300-dccd-11eb-90d9-dd044014fc0d.png)

### 2.1 jvm内存模型


**JVM内存结构**
> 堆：线程共享。主要用于用于存放**对象实例**。绝大多数创建的对象都会被存放到这里。 类的静态变量则和类定义 ，对象的成员变量与对象本身，包装类型（例如 Byte，Integer，Long 等）</br>
 `逃逸分析`： （除了部分由于逃逸分析而在对外分配的对象，该部分只是在方法体被引用，故被分配到了栈上）
  栈：线程私有。所有原生类型的局部变量
- 每个线程都只能访问自己的线程栈，都不能访问（看不见）其他线程的局部变量。

- 线程可以将一个原生变量值的副本传给另一个线程，但不能共享原生局部变量本身。

- 不管是创建一个对象并将其赋值给局部变量， 还是赋值给另一个对象的成员变量， 创建的对象都会被保存到堆内存中。

- 如果是对象引用，则栈中的局部变量槽位中保存着对象的引用地址，而实际的对象内容保存在堆中。


**内存数据**
> 方法中使用的原生数据类型和对象引用地址在栈上存储；对象、对象成员与类定义、静态变量在堆上 
</br>
> 如果是成员变量，那么不分基本类型和引用类型都是在java的堆内存里面分配空间，而局部变量的基本类型是在栈上分配的。栈属于线程私有的空间，局部变量的生命周期和作用域一般都很短，为了提高gc效率，所以没必要放在堆里面。


**堆的分代**
> 为了提高对象内存分配和垃圾回收的效率，jvm划分堆的不同区域  </br>
> 年轻代（young）、老年代（old）、永久区（permanent). 对HotSpot虚拟机而言，JDK1.8之后为metaspace（元区间）替代永久代 </br>
> 静态属性、类信息等存放在永久代中，新生代中的对象存活时间短，只需要在新生代区域中频繁进行GC，老年代中对象生命周期长，内存回收的频率相对较低，不需要频繁进行回收

**年轻代**
> 可以分为 Eden，form survivor，to survivor.默认比例为8：1：1。主要采用复制算法回收 </br>
> 充分利用内存空间，减少浪费。新生成的对象在Eden区分配（大对象除外，大对象直接进入老年代），当Eden区没有足够的空间进行分配时，虚拟机将发起一次Minor GC。  </br>
注： 当没有发生minor gc时，s0和s1只有一块会分配内存

**永久代**

> 在新生代中经历了多次（具体看虚拟机配置的阀值，默认15）GC后仍然存活下来的对象会进入老年代中。老年代中的对象生命周期较长，存活率比较高，在老年代中进行GC的频率相对而言较低，而且回收的速度也比较慢。

**堆异常**

`java.lang.OutOfMemoryError: Java heap space`，Java虚拟机的堆内存不够。原因：
> 1、Java虚拟机的堆内存设置不够，可以通过参数-Xms、-Xmx来调整。 </br>
  2、代码中创建了大量大对象，并且长时间不能被垃圾收集器收集（存在被引用）。

`java.lang.OutOfMemoryError: PermGen space` ，java虚拟机的永久代内存不够。原因：
1、程序启动需要加载大量的第三方jar包。例如：在一个Tomcat下部署了太多的应用。
2、大量动态反射生成的类不断被加载，最终导致Perm区被占满

**栈异常**

`java.lang.StackOverflowError` ，java栈空间不足。原因：
1.递归过深



**JNI**
  JNI是Java Native Interface的缩写，通过使用 Java本地接口书写程序，可以确保代码在不同的平台上方便移植
- 每启动一个线程，JVM 就会在栈空间栈分配对应的 线程栈, 比如 1MB 的空间（-Xss1m）
- 线程栈也叫做 Java 方法栈。 如果使用了JNI 方法，则会分配一个单独的本地方法栈(Native Stack)

**非堆区域**

> 元数据区：存放常量池 和 方法区
> CCS： Compressed Class Space, 存放 class 信息的，和 Metaspace 有交叉
> Code cache：  存放 JIT 编译器编译后的本地机器代码  


**CPU乱序执行**

**volatile关键字**

**原子性操作**

**内存屏障**

---

### 2.2 jvm启动参数


**常见启动参数**
 标色代表常用

> -`Xmx`, 指定最大堆内存。 如 -Xmx4g. 这只是限制了 Heap 部分的最大值为4g。
> 这个内存不包括栈内存，也不包括堆外使用的内存。

> -`Xms`, 指定堆内存空间的初始大小。 如 -Xms4g。 而且指定的内存大小，并
> 不是操作系统实际分配的初始值，而是GC先规划好，用到才分配。 专用服务
> 器上需要保持 –Xms 和 –Xmx 一致，否则应用刚启动可能就有好几个 FullGC。
> 当两者配置不一致时，堆内存扩容可能会导致性能抖动

> -Xmn, 等价于 -XX:NewSize，使用 G1 垃圾收集器不应该设置该选项，在其
> 他的某些业务场景下可以设置。官方建议设置为 -Xmx 的 1/2 ~ 1/4.

> -`XX：MaxPermSize=size`, 这是 JDK1.7 之前使用的。Java8 默认允许的
> Meta空间无限大，此参数无效。

> -`XX：MaxMetaspaceSize=size`, Java8 默认不限制 Meta 空间, 一般不允许设
> 置该选项。

> -XX：MaxDirectMemorySize=size，系统可以使用的最大堆外内存，这个参
> 数跟 -Dsun.nio.MaxDirectMemorySize 效果相同。

> -`Xss, 设置每个线程栈的字节数`。 例如 -Xss1m 指定线程栈为 1MB，与-
> XX:ThreadStackSize=1m 等价

> -`XX：+UseG1GC`：使用 G1 垃圾回收器

> -`XX：+UseConcMarkSweepGC`：使用 CMS 垃圾回收器

> -`XX：+UseSerialGC`：使用串行垃圾回收器

串行 GC 对年轻代使用 mark-copy（标记-复制） 算法，对老年代使用 mark-sweep-compact
（标记-清除-整理）算法。

两者都是单线程的垃圾收集器，不能进行并行处理，所以都会触发全线暂停（STW），停止所
有的应用线程。

因此这种 GC 算法不能充分利用多核 CPU。不管有多少 CPU 内核，JVM 在垃圾收集时都只能使
用单个核心。

CPU 利用率高，暂停时间长。简单粗暴，就像老式的电脑，动不动就卡死。
该选项只适合几百 MB 堆内存的 JVM，而且是单核 CPU 时比较有用。

> -`XX：+UseParallelGC`：使用并行垃圾回收器

> -XX：+USeParNewGC 改进版本的 Serial GC，可以配合 CMS 使用。
> -XX：+UseParallelGC
> -XX：+UseParallelOldGC
> -XX：+UseParallelGC -XX:+UseParallelOldGC

• 年轻代和老年代的垃圾回收都会触发 STW 事件

• 在年轻代使用 标记-复制（mark-copy）算法，在老年代使用 标记-清除-整理（mark-sweepcompact）算法。

> -XX：ParallelGCThreads=N 来指定 GC 线程数， 其默认值为 CPU 核心数。

并行垃圾收集器适用于多核服务器，主要目标是增加吞吐量。因为对系统资源的有效使用，能达到
更高的吞吐量

在 GC 期间，所有 CPU 内核都在并行清理垃圾，所以总暂停时间更短；

在两次 GC 周期的间隔期，没有 GC 线程在运行，不会消耗任何系统资源



// Java 11+

> -`XX：+UnlockExperimentalVMOptions -XX:+UseZGC`    解锁实验参数，并使用zgc

// Java 12+

> -`XX：+UnlockExperimentalVMOptions -XX:+UseShenandoahGC`   解锁实验参数，并使用ShenandoahGC

> -`XX：+-HeapDumpOnOutOfMemoryError` 选项, 当 OutOfMemoryError 产生，即内存溢出(堆内存或持久代)时，
> 自动 Dump 堆内存。
> 示例用法： java -XX:+HeapDumpOnOutOfMemoryError -Xmx256m ConsumeHeap

> -XX：HeapDumpPath 选项, 与 HeapDumpOnOutOfMemoryError 搭配使用, 指定内存溢出时 Dump 文件的目
> 录。
> 如果没有指定则默认为启动 Java 程序的工作目录。
> 示例用法： java -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/ ConsumeHeap 
> 自动 Dump 的 hprof 文件会存储到 /usr/local/ 目录下。

> -XX：OnError 选项, 发生致命错误时（fatal error）执行的脚本。
> 例如, 写一个脚本来记录出错时间, 执行一些命令, 或者 curl 一下某个在线报警的 url. 
> 示例用法：java -XX:OnError="gdb - %p" MyApp 
> 可以发现有一个 %p 的格式化字符串，表示进程 PID。 

> -`XX：OnOutOfMemoryError` 选项, 抛出 OutOfMemoryError 错误时执行的脚本。

> -XX：ErrorFile=filename 选项, 致命错误的日志文件名,绝对路径或者相对路径。

> -`Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=1506`，远程调试
> -`XX：+MaxTenuringThreshold=15` 对象在s0和s1中复制的周期

推荐jvm参数自动生成网站： [perfma](https://opts.console.perfma.com/)

> -`XX：+UseConcMarkSweepGC`
>  cms gc.其对年轻代采用并行 STW 方式的 mark-copy (标记-复制)算法，对老年代主要使用并发 marksweep (标记-清除)算法。

元空间

```text
XX:MetaspaceSize
    class metadata的初始空间配额，以bytes为单位，达到该值就会触发垃圾收集进行类型卸载，
    同时GC会对该值进行调整：如果释放了大量的空间，就适当的降低该值；如果释放了很少的空间，
    那么在不超过MaxMetaspaceSize（如果设置了的话），适当的提高该值。
-XX：MaxMetaspaceSize
    可以为class metadata分配的最大空间。默认是没有限制的。
-XX：MinMetaspaceFreeRatio
    在GC之后，最小的Metaspace剩余空间容量的百分比，减少为class metadata分配空间导致的垃圾收集。
-XX:MaxMetaspaceFreeRatio
    在GC之后，最大的Metaspace剩余空间容量的百分比，减少为class metadata释放空间导致的垃圾收集。
```






**各个版本jvm默认的gc**

1.jdk7  Parallel

2.jdk8  Parallel

3.jdk9   G1，[jdk9官方文档](https://docs.oracle.com/javase/9/gctuning/introduction-garbage-collection-tuning.htm#JSGCT-GUID-223394DF-2E27-4F5D-A7DF-83151EB577BB)



**Agent**




---





### 2.3 jvm命令行工具


**常见命令行**

> jinfo  
  jinfo可以查看设置的jvm的信息，



> jps

查看java进程
 -l 查看整个类名

![jps使用](https://user-images.githubusercontent.com/24818340/124375566-7f7d6080-dcd5-11eb-83ad-22f651624e97.png)

> jstat

查看 JVM 内部 gc 相关信息，常见命令:

-   jstat -gc  统计内存情况
-   jstat -gcnew pid   新生代统计
-   jstat -gccapacity pid  新生代内存统计
-   jstat -gcutil pid    1000  20   不展示容量  ，只展示使用率   每隔1s展示20s的gc情况

![jstat使用](https://user-images.githubusercontent.com/24818340/124375567-8015f700-dcd5-11eb-92f6-7ab2ceeb6283.png)
![jstat使用](https://user-images.githubusercontent.com/24818340/124375759-6e811f00-dcd6-11eb-88e1-2611569b4645.png)

> jmap

查看 heap 或类占用空间统计

- jmap  -histo  pid| more  查看所有对象及其实例

- jmap  -histo:live  pid | more  查看所有存活10例

- jmap  -histo pid | sort -n -r -k 2 | head -10     

- jmap  -histo pid | sort -n -r -k 2 | head -10


![jmap使用](https://user-images.githubusercontent.com/24818340/124375568-8015f700-dcd5-11eb-9c2d-3a7458ccd3a1.png)


**堆默认大小**
  </br>
> 最大物理内存大小不超过192兆字节（MB）时默认最大堆大小是物理内存的一半，否则占用物理内存的四分之一

> 在32位JVM上，如果有4 GB或更多的物理内存，则默认的最大堆大小最多可以为1 GB。在64位JVM上，如果有128
GB或更多的物理内存，则默认的最大堆大小最大为32 GB。

> 在JVM初始化期间分配了一个较小的值，称为初始堆大小。此数量至少为8 MB，否则为物理内存的1/64，最大为1 GB。
分配给年轻代的最大空间量是堆总大小的三分之一，即年轻代和老年代默认的比例是1:2

> 您可以使用-Xms（初始堆大小）和-Xmx（最大堆大小）来指定初始堆大小和最大堆大小。如果你知道你的应用程序有多少堆需要工作做好，你可以设置-Xms和-Xmx相同的值。否则，JVM将使用初始堆大小开始，然后将增大Java堆，直到找到堆使用率和性能之间的平衡为止。



> jstack

查看线程信息

- 1
- 2
- 3

![jstack使用](https://user-images.githubusercontent.com/24818340/124375565-7e4c3380-dcd5-11eb-9513-f100c543120c.png)


**jstack排查堆栈异常**
1.  top 查看所有进程
2.  top -Hp pid （shift   +m 内存排序  +p cpu排序）
3.  printf '%x\n'  pid    # 格式化线程名为16进制，如ceb1
4.  jstack pid > hello.dump  输出dump
5.  cat hello.dump | grep  ceb1


> jjs

执行js的命令行界面

> jcmd

执行 JVM 相关分析命令（整合命令）


> jvisualvm

图形化工具

> jconsole

图形化工具

> jmc 

图形化工具。飞行控制台




---





### 2.4 jvm垃圾回收


**垃圾回收算法**

1. 引用计数法

给对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加1，当引用失效时，计数器就减1。任何时刻计数器为0的对象就是不再被使用的。
缺点： 不能解决循环依赖问题，A依赖B，B依赖A；每次对对象赋值均要维护引用计数器，且计数器本身也有一定消耗


2. 可达性算法

算法的基本思路就是通过一系列称为GC Roots的对象作为起始点，从这些节点开始向下搜索，搜索走过的路径被称为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象不可用的。

> 可以作为GC Roots的对象

虚拟机栈（栈帧中的本地变量表）中引用的对象
方法区中类静态属性引用的对象
方法区中常量引用的对象
本地方法栈中JNI（即一般说的Native方法）引用的对象


3. 复制算法
    年轻代默认使用复制。年轻代分为eden , s0 , s1.

![年轻代复制](https://user-images.githubusercontent.com/24818340/124377334-09c9c280-dcde-11eb-9ec8-ad82ba3cbd50.png)
优点：
缺点：

4. 标记清除

老年代默认都是存活对象，采用移动方式：
1. 标记所有通过 GC roots 可达的对象；
2. 删除所有不可达对象；
3. 整理老年代空间中的内容，方法是将所有的存
活对象复制，从老年代空间开始的地方依次存
放

优点：
缺点：


5. 标记清除整理

优点：
缺点：


**年轻代复制，老年代移动的原因**

**CMS-GC**

   CMS GC 的设计目标是避免在老年代垃圾收集时出现长时间的卡顿，主要通过两种手段来达成此目标：
 1. 不对老年代进行整理，而是使用空闲列表（free-lists）来管理内存空间的回收。
 2. 在 mark-and-sweep （标记-清除） 阶段的大部分工作和应用线程一起并发执行
</br>
      主要步骤
</br>
- 1: `Initial Mark（初始标记）`     ------ stop the world

   初始标记的目标是标记所有的根对象，包括根对象直接引用的对象，以及被年轻代中所有存活对象所引用的对象（老年代单独回收）

- 2: `Concurrent Mark（并发标记）`

   在此阶段，CMS GC 遍历老年代，标记所有的存活对象，从前一阶段 “Initial Mark” 找到的根对象开始算起。 “并发标记”阶段，就是与应用程序同时运行，不用暂停的阶段。

- 3: `Concurrent Preclean（并发预清理）`

  此阶段同样是与应用线程并发执行的，不需要停止应用线程。 因为前一阶段【并发标记】与程序并发运行，可能有一些引用关系已经发生了改变。如果在并发标记过程中引用关系发生了变化，JVM 会通过“Card（卡片）”的方式将发生了改变的区域标记为“脏”区，这就是所谓的 卡片标记（Card Marking）。

- 4: `Final Remark（最终标记）`     ------ stop the world
  
  最终标记阶段是此次 GC 事件中的第二次（也是最后一次）STW 停顿。本阶段的目标是完成老年代中所有存活对象的标记. 因为之前的预清理阶段是并发执行的，有可能 GC 线程跟不上应用程序的修改速度。所以需要一次STW 暂停来处理各种复杂的情况。通常 CMS 会尝试在年轻代尽可能空的情况下执行 Final Remark 阶段，以免连续触发多次 STW 事件。

- 5: `Concurrent Sweep`（并发清除）
  此阶段与应用程序并发执行，不需要 STW 停顿。JVM 在此阶段删除不再使用的对象，并回收他们占用的内存空间。

- 6: `Concurrent Reset`（并发重置）

**并行和并发区别**

- 并行  单个或者多个cpu执行任务，用户线程隔离
- 并发  单个cpu执行多项任务，用户线程和任务线程一起竞争cpu资源

**G1-GC**

· G1 的全称是 Garbage-First，意为垃圾优先，哪一块的垃圾最多就优先清理它。
· G1-GC 最主要的设计目标是：将 STW 停顿的时间和分布，变成可预期且可配置的。

> 首先，堆不再分成年轻代和老年代，而是划分为多个（通常是 2048 个）可以存放对象的 小块堆区域(smaller heap regions)。每个小块，可能一会被定义成 Eden 区，一会被指定为 Survivor区或者
Old 区。在逻辑上，所有的 Eden 区和 Survivor
区合起来就是年轻代，所有的 Old 区拼在一起那
就是老年代


`-XX：+UseG1GC -XX:MaxGCPauseMillis=50`

> 这样划分之后，使得 G1 不必每次都去收集整
个堆空间，而是以增量的方式来进行处理: 每
次只处理一部分内存块，称为此次 GC 的回收
集(collection set)。每次 GC 暂停都会收集所
有年轻代的内存块，但一般只包含部分老年代
的内存块。

</br>

> G1 的另一项创新是，在并发阶段估算每个小
堆块存活对象的总数。构建回收集的原则是：
垃圾最多的小块会被优先收集。这也是 G1 名
称的由来。


**-XX：+UseG1GC**：启用 G1 GC；

**-XX：G1NewSizePercent**：初始年轻代占整个 Java Heap 的大小，默认值为 5%；
**
-XX：G1MaxNewSizePercent**：最大年轻代占整个 Java Heap 的大小，默认值为 60%；

**-XX：G1HeapRegionSize**：设置每个 Region 的大小，单位 MB，需要为 1，2，4，8，16，32 中的某个值，默 认是堆内存的 1/2000。如果这个值设置比较大，那么大对象就可以进入 Region 了。

**-XX：ConcGCThreads**：与 Java 应用一起执行的 GC 线程数量，默认是 Java 线程的 1/4，减少这个参数的数值可 能会提升并行回收的效率，提高系统内部吞吐量。如果这个数值过低，参与回收垃圾的线程不足，也会导致并行回
收机制耗时加长。

**-XX：+InitiatingHeapOccupancyPercent（简称 IHOP）**：G1 内部并行回收循环启动的阈值，默认为 Java Heap 的 45%。这个可以理解为老年代使用大于等于 45% 的时候，JVM 会启动垃圾回收。这个值非常重要，它决定了在 什么时间启动老年代的并行回收。

**-XX：G1HeapWastePercent**：G1停止回收的最小内存大小，默认是堆大小的 5%。GC 会收集所有的 Region 中 的对象，但是如果下降到了 5%，就会停下来不再收集了。就是说，不必每次回收就把所有的垃圾都处理完，可以 遗留少量的下次处理，这样也降低了单次消耗的时间。

-XX：G1MixedGCCountTarget：设置并行循环之后需要有多少个混合 GC 启动，默认值是 8 个。老年代 Regions 的回收时间通常比年轻代的收集时间要长一些。所以如果混合收集器比较多，可以允许 G1 延长老年代的收集时间。

-XX：+G1PrintRegionLivenessInfo：这个参数需要和 -XX:+UnlockDiagnosticVMOptions 配合启动，打印 JVM 的调试信
息，每个 Region 里的对象存活信息。

-XX：G1ReservePercent：G1 为了保留一些空间用于年代之间的提升，默认值是堆空间的 10%。因为大量执行回收的地方在
年轻代（存活时间较短），所以如果你的应用里面有比较大的堆内存空间、比较多的大对象存活，这里需要保留一些内存。

-XX：+G1SummarizeRSetStats：这也是一个 VM 的调试信息。如果启用，会在 VM 退出的时候打印出 Rsets 的详细总结信
息。如果启用 -XX:G1SummaryRSetStatsPeriod 参数，就会阶段性地打印 Rsets 信息。

-XX：+G1TraceConcRefinement：这个也是一个 VM 的调试信息，如果启用，并行回收阶段的日志就会被详细打印出来。

**-XX：+GCTimeRatio**：这个参数就是计算花在 Java 应用线程上和花在 GC 线程上的时间比率，默认是 9，跟新生代内存的分
配比例一致。这个参数主要的目的是让用户可以控制花在应用上的时间，G1 的计算公式是 100/（1+GCTimeRatio）。这样
如果参数设置为 9，则最多 10% 的时间会花在 GC 工作上面。Parallel GC 的默认值是 99，表示 1% 的时间被用在 GC 上面，
这是因为 Parallel GC 贯穿整个 GC，而 G1 则根据 Region 来进行划分，不需要全局性扫描整个内存堆。

-XX：+UseStringDeduplication：手动开启 Java String 对象的去重工作，这个是 JDK8u20 版本之后新增的参数，主要用于
相同 String 避免重复申请内存，节约 Region 的使用。

**-XX：MaxGCPauseMills**：预期 G1 每次执行 GC 操作的暂停时间，单位是毫秒，默认值是 200 毫秒，G1 会尽量保证控制在
这个范围内

</br>
步骤：

**·阶段 1: Initial Mark（初始标记）**

此阶段标记所有从 GC 根对象直接可达的对象

**·阶段 2: Root Region Scan（Root区扫描）**

此阶段标记所有从 "根区域" 可达的存活对象。根区域包括：非空的区域，以及在标记过程中不得不收集的区域。

**·阶段 3: Concurrent Mark（并发标记）**

此阶段和 CMS 的并发标记阶段非常类似：只遍历对象图，并在一个特殊的位图中标记能访问到的对象

**·阶段 4: Remark（再次标记）** 

和 CMS 类似，这是一次 STW 停顿(因为不是并发的阶段)，以完成标记过程。 G1 收集器会短暂地停止应用线程，
停止并发更新信息的写入，处理其中的少量信息，并标记所有在并发标记开始时未被标记的存活对象。

**·阶段 5: Cleanup（清理）**

最后这个清理阶段为即将到来的转移阶段做准备，统计小堆块中所有存活的对象，并将小堆块进行排序，以提升
GC 的效率，维护并发标记的内部状态。 所有不包含存活对象的小堆块在此阶段都被回收了。有一部分任务是并发
的：例如空堆区的回收，还有大部分的存活率计算。此阶段也需要一个短暂的 STW 暂停


**不同GC对比**

![gc对比](https://user-images.githubusercontent.com/24818340/124379748-61225f80-dceb-11eb-9134-47f56301cfbe.png)


# 2.5 总结

- 串行：单个线程执行垃圾回收，并且此时用户线程仍然处于等待状态。
- 并行：指多条垃圾收集线程并行工作，但此时用户线程仍然处于等待状态。
- 并发：指用户线程与垃圾收集线程同时执行（但不一定是并行的，可能会交替执行），用户程序在继续运行，而垃圾收集程序运行于另一个CPU上。

| 名称        | 串行/并行/并发             | 回收算法 | 适用场景              |
| ----------- | -------------------------- | -------- | --------------------- |
| SerialOldGC | 串行                       | 标记整理 | 单cpu                 |
| ParNewOldGC | 并行                       | 标记整理 | 多cpu                 |
| CMS         | 并发，几乎不会暂停用户线程 | 标记清除 | 多cpu且与用户线程共存 |

**CMS的优点和缺点**

CMS收集器的优点：并发收集、低停顿，但是CMS还远远达不到完美，主要有三个显著缺点：cpu敏感，浮动垃圾，空间碎片

CMS默认启动的回收线程数是：(CPU数量+3) / 4。

​     [由于CMS并发清理阶段用户线程还在运行，伴随程序的运行自热会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，CMS无法在本次收集中处理它们，只好留待下一次GC时将其清理掉。这一部分垃圾称为“浮动垃圾](https://blog.csdn.net/h2604396739/article/details/87709160)

　最后一个缺点，CMS是基于“标记-清除”算法实现的收集器，使用“标记-清除”算法收集后，会产生大量碎片。空间碎片太多时，将会给对象分配带来很多麻烦，比如说大对象，内存空间找不到连续的空间来分配不得不提前触发一次Full  GC。为了解决这个问题，CMS收集器提供了一个-XX:UseCMSCompactAtFullCollection开关参数，用于在Full  GC之后增加一个碎片整理过程，还可通过-XX:CMSFullGCBeforeCompaction参数设置执行多少次不压缩的Full  GC之后，跟着来一次碎片整理过程。
　最后一个缺点，CMS是基于“标记-清除”算法实现的收集器，使用“标记-清除”算法收集后，会产生大量碎片。空间碎片太多时，将会给对象分配带来很多麻烦，比如说大对象，内存空间找不到连续的空间来分配不得不提前触发一次Full  GC。为了解决这个问题，CMS收集器提供了一个-XX:UseCMSCompactAtFullCollection开关参数，用于在Full  GC之后增加一个碎片整理过程，还可通过-XX:CMSFullGCBeforeCompaction参数设置执行多少次不压缩的Full  GC之后，跟着来一次碎片整理过程。







