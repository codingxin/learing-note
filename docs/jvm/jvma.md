# 一.JVM

##  1  jvm基础
   主要知识点：
>  jvm基础知识    </br>
>  java字节码技术 </br>
>  jvm类加载器    </br>
### 1.1 jvm基础知识
**jvm介绍**
> Java 是一种**面向对象**、**静态类型**、**编译执行**，运行时、**跨平台**的高级语言

**java数据类型**
- Java虚拟机中，数据类型可以分为两类：**基本类型**和**引用类型**。基本类型的变量保存原始值，即：他代表的值就是数值本身；而引用类型的变量保存引用值。“引用值”代表了某个对象的引用，而不是对象本身，对象本身存放在这个引用值所表示的地址的位置

> 基本类型包括：byte、short、int、long、char、float、double、Boolean；引用类型包括：类类型、接口类型、数组  </br>

**java堆栈作用**
> 栈代表了处理逻辑，而堆代表了数据。对象的属性其实就是数据，存放在堆中；而对象的行为（方法），就是运行逻辑，放在栈中
-  堆
   堆中存的是对象 
-  栈
   栈中存的是基本数据类型和堆中对象的引用

**java是值传递吗?**
> 基本类型和引用的处理是一样的，都是传值

**引用类型**

**对象大小**


**什么是字节码?采用字节码的好处是什么?**
> 在 Java 中，JVM 可以理解的代码就叫做`字节码`（即扩展名为 `.class` 的文件），它不面向任何特定的处理器，只面向虚拟机。Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以 Java 程序运行时比较高效，而且，由于字节码并不针对一种特定的机器，因此，Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。

---

### 1.2 字节码知识
**什么是字节码?采用字节码的好处是什么?**
> `Java bytecode 由单字节（byte）的指令组成，理论上最多支持 256 个操作码（opcode）`。

**根据指令性质分类**
1. 栈操作指令，包括与局部变量交互的指令
2. 程序流程控制指令
3. 对象操作指令，包括方法调用指令
4. 算术运算以及类型转换指令

**查看字节码** 

`javac hello.java` 得到hello.class </br>
`javap hello.class` 得到字节码文件  javap -v 展示详细信息

**字节码运行结构**
 JVM 是一台基于栈的计算机器。每个线程都有一个独属于自己的线程栈（JVM Stack），用于存储栈帧（Frame）。每一次方法调用，JVM 都会自动创建一个栈帧。栈帧由操作数栈， 局部变量数组以及一个 Class 引用组成。Class 引用 指向当前方法在运行时常量池中对应的 Class

**常见字节码**

- iconst  定义变量

- istore   存储在局部变量区

- iload   加载到求值栈

- if_icmpge  if判断

- aload

### 1.3 jvm类加载器

**类的生命周期**

- 加载（Loading）：找 Class 文件

- 验证（Verification）：验证格式、依赖

- 准备（Preparation）：静态字段、方法表

- 解析（Resolution）：符号解析为引用

- 初始化（Initialization）：构造器、静态变量赋值、静态代码块

- 使用（Using）

- 卸载（Unloading）


**类的加载时机**

- 当虚拟机启动时，初始化用户指定的主类，就是启动执行的 main 方法所在的类；

- 当遇到用以新建目标类实例的 new 指令时，初始化 new 指令的目标类，就是 new一个类的时候要初始化；

- 当遇到调用静态方法/静态字段的指令时，初始化该静态方法所在的类；

- 子类的初始化会触发父类的初始化；

- 如果一个接口定义了 default 方法，那么直接实现或者间接实现该接口的类的初始化，会触发该接口的初始化；

- 使用反射 API 对某个类进行反射调用时，初始化这个类，其实跟前面一样，反射调用要么是已经有实例了，要么是静态方法，都需要初始化；

- 当初次调用 MethodHandle 实例时，初始化该 MethodHandle 指向的方法所在的
类。


**不会初始化（可能会加载）**

- 通过子类引用父类的静态字段，只会触发父类的初始化，而不会触发子类的初始化。

- 定义对象数组，不会触发该类的初始化。

- 常量在编译期间会存入调用类的常量池中，本质上并没有直接引用定义常量的类，不会触发定义常量所在的类。

- 通过类名获取 Class 对象，不会触发类的初始化，Hello.class 不会让 Hello 类初始
化。

- 通过 Class.forName 加载指定类时，如果指定参数 initialize 为 false 时，也不会触发类初始化，其实这个参数是告诉虚拟机，是否要对类进行初始化。Class.forName（“jvm.Hello”）默认会加载 Hello 类。

- 通过 ClassLoader 默认的 loadClass 方法，也不会触发初始化动作（加载了，但是不初始化）。

**类加载器及其特点**

![jvm](https://user-images.githubusercontent.com/24818340/124374461-f57dc980-dccd-11eb-868e-45c59c006c79.png)

 1.分类：
- 启动类加载器（BootstrapClassLoader）
- 扩展类加载器（ExtClassLoader）
- 应用类加载器（AppClassLoader）
- 用户定义类加载器
 2.特点：
- 双亲委托
- 负责依赖
- 缓存加载


---




