##### jvm内存屏障java

- [https://zhuanlan.zhihu.com/p/487453938](https://zhuanlan.zhihu.com/p/487453938)
- [https://juejin.cn/post/6844904079978659848](https://juejin.cn/post/6844904079978659848)
- https://monkeysayhi.github.io/2017/12/28/%E4%B8%80%E6%96%87%E8%A7%A3%E5%86%B3%E5%86%85%E5%AD%98%E5%B1%8F%E9%9A%9C/
- https://www.jianshu.com/p/2ab5e3d7e510

##### minor gc full gc 区别(*)

​	[https://blog.csdn.net/u010796790/article/details/52213708](https://blog.csdn.net/u010796790/article/details/52213708)

##### **JVM死锁工具**

- Jconsole , Jstack, visualVM

##### **sycn的锁升级过程**

- 无锁状态，偏向锁状态，轻量级锁状态和重量级锁状态
- 偏向锁
  - 偏向锁的目的是在某个线程获得锁之后，消除这个线程锁重入（CAS）的开销，看起来让这个线程得到了偏护
- 自旋锁

##### JVM**内存配置参数**

- -Xmx Java Heap最大值，默认值为物理内存的1/4，最佳设值应该视物理内存大小及计算机内其他内存开销而定；
- -Xms Java Heap初始值，Server端JVM最好将-Xms和-Xmx设为相同值，开发测试机JVM可以保留默认值；
- -Xmn Java Heap Young区大小，不熟悉最好保留默认值；
- -Xss 每个线程的Stack大小，不熟悉最好保留默认值；

**运行时数据区**

​	**堆，本地方法栈 虚拟机栈，程序计数器，方法区**

- 程序计数器（线程独享)

  - 程序计数器（Program Counter Register)是一块很小的内存区域，它可以看做是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型中，字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。

- 虚拟机栈（线程独享)

  - Java虚拟机栈也是线程私有的，它的生命周期与线程相同。虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同时都会创建一个栈帧（StackFrame)用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每一个方法从调用到执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。

- 本地方法栈（线程独享)

  - 本地方法栈（Native Method Stack)与虚拟机栈所发挥的作用是非常相似的，它们之间的区别不过是虚拟机栈为虚拟机执行Java方法（字节码)服务，而本地方法栈为虚拟机使用到的Native方法服务。

- Java堆

  - 对大多数应用来说，Java堆是Java虚拟机所管理的内存中最大的一块。Java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的就是存放对象实例。

  - Java堆是垃圾收集器管理的主要区域。从内存回收的角度来看，由于现在收集器基本都采用分代收集算法，所以Java堆中还可以分为：新生代和老年代；再细致一点的有Eden空间、From Survivor空间、To Survivor空间等。从内存分配的角度来看，线程共享的Java堆中可能划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer，TLAB)。

  - Java堆可以处于物理上不连续的内存空间中，只要逻辑上是连续的即可。

- 方法区

- 直接内存

- 对象的创建

- 对象的内存布局

  - **对象头（Header)、实例数据（Instance Data)和对齐填充（Padding)。**

- 对象的访问定位

- 内存溢出与内存泄露

- 栈溢出（虚拟机栈和本地方法栈)

- 堆溢出

-  方法区溢出

- 直接内存溢出

- GC

  - 引用计数算法

  - 可达性分析算法

- GC算法

  - 标记-清除算法

  - 复制算法

  - 标记-整理算法

  - 分代收集算法

- Minor Full GC

- 

- HotSpot的垃圾收集器

  - Serial垃圾收集器

  - ParNew垃圾收集器

  - .Parallel Scavenge收集器

  - Serial Old收集器

- G1收集器（重点)

- 

- 内存分配原则

  - 对象优先在Eden分配

  - 大对象直接进入老年代

  - 长期存活的对象将进入老年代

  - 动态对象年龄判定

  - 空间分配担保

- GC相关API

  - System.gc

- 类加载机制

-  对象初始化的先后顺序

- 

- 性能监控与故障处理工具

  - JPS 
    - 显示所有虚拟机进程

  - JConsole
    - 图形化工具，查询JVM中的内存变化情况。

  - JVisualVM 
    - 图形化工具，分析GC趋势、内存消耗情况

  - JMap
    - 命令行工具，查看JVM中各个代的内存状况、JVM中对象的内存的占用状况，以及dump整个JVM中的内存信息。

  - JHat 用于分析JVM堆的dump文件：jhat –J-Xmx1024M [file]
    - 可以通过浏览器访问，端口号是7000.

  - JStack：看到JVM中线程的运行状况，包括锁的等待、线程是否在运行等

  -  JStat：JVM统计监测工具

  - MAT 可视化分析dump文件

- 关于垃圾回收器

  - 最开始的是Serial 收集器，是单线程的，工作时候用户线程停止，适合客户端应用，比如桌面应用。继而有了ParNew收集器，和Serial一样，但是是多线程回收。继而是JDK5发布时候的CMS（conconcurent mark Swap）（老年代的收集器）。真正意义上的第一个支持并发的垃圾回收，和用户线程同时工作。遗憾的是没有办法和 Parallel Scavenge 一起工作。最新的就是G1垃圾回收，不需要其他的新生代收集器的配合工作。这是面向全堆的垃圾回收。

  - Parallel Scavenge 

    - 吞吐量优先和自适应。

    - 可以设置响应的参数，具体百度

  - Serial Old
    - Serial 收集器的老年代版本

  - Parallel Old 收集器 
    - Parallel Scavenge 收集器的老年代版本

- G1回收器

  - 十年磨一剑 ，从论文出来到2012年才正式发布

- https://pdai.tech/md/java/jvm/java-jvm-gc-zgc.html

