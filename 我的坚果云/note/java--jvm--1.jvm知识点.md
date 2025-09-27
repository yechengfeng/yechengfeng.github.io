##### jvm内存屏障java

- [https://zhuanlan.zhihu.com/p/487453938](https://zhuanlan.zhihu.com/p/487453938)
- [https://juejin.cn/post/6844904079978659848](https://juejin.cn/post/6844904079978659848)
- [https://monkeysayhi.github.io/2017/12/28/%E4%B8%80%E6%96%87%E8%A7%A3%E5%86%B3%E5%86%85%E5%AD%98%E5%B1%8F%E9%9A%9C/](https://monkeysayhi.github.io/2017/12/28/%E4%B8%80%E6%96%87%E8%A7%A3%E5%86%B3%E5%86%85%E5%AD%98%E5%B1%8F%E9%9A%9C/)
- [https://www.jianshu.com/p/2ab5e3d7e510](https://www.jianshu.com/p/2ab5e3d7e510)

##### minor gc full gc 区别(*)

​	[https://blog.csdn.net/u010796790/article/details/52213708](https://blog.csdn.net/u010796790/article/details/52213708)



##### JVM**内存配置参数**

JVM调优参考 [https://www.jianshu.com/p/a2a6a0995fee](https://www.jianshu.com/p/a2a6a0995fee)

参考配置

```shell
java -Xms4g -Xmx4g -Xmn2g \
     -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=512m \
     -XX:+UseG1GC -XX:MaxGCPauseMillis=200 \
     -XX:InitiatingHeapOccupancyPercent=45 \
     -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/logs/heapdump.hprof \
     -Xlog:gc*,gc+heap=debug:file=/logs/gc.log:time,uptimemillis:filecount=5,filesize=100m \
     -Dfile.encoding=UTF-8 \
     -jar your-application.jar
```

JVM会根据初始堆和最大堆设置默认值，一般默认最大堆为物理堆的四分之一，初始堆内存为64分之一

> 查看方法
>
> ```shell
>    java -XX:+PrintFlagsFinal -version | grep MaxHeapSize;  查看最大堆默认值
>  	 java -XX:+PrintFlagsFinal -version | grep InitialHeapSize 查看初始堆默认值
>  	
> ```
>
> 生产环境必须显示声明
>
> - 避免资源浪费
> - 防止内存溢出
> - 稳定性需求
>   - 默认的最大堆内存和最小堆内存可能不足，容易引发GC波动，生产环境一般配置相同，避免堆震荡
> - 在哪里配置
>   - 启动参数
>   - 系统环境变量
>   - Maven pom文件
>   - tomcat 
>   - jetty

- -Xmx

  >  Java Heap最大值，默认值为物理内存的1/4，最佳设值应该视物理内存大小及计算机内其他内存开销而定；

- -Xms

  >  Java Heap初始值，Server端JVM最好将-Xms和-Xmx设为相同值，开发测试机JVM可以保留默认值；

- -Xmn 

  > Java Heap Young区大小，不熟悉最好保留默认值；

- -Xss 

  > 每个线程的Stack大小，不熟悉最好保留默认值；

**运行时数据区**

[虚拟机概述](https://share.note.youdao.com/ynoteshare/index.html?id=04255aa8ae9ab45ed502296d57c736d3&type=note&_time=175462764166)

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

  > [https://share.note.youdao.com/ynoteshare/index.html?id=720b1f1bef16d494b767edeaf52e1eee&type=note&_time=1754630816161](https://share.note.youdao.com/ynoteshare/index.html?id=720b1f1bef16d494b767edeaf52e1eee&type=note&_time=1754630816161)

- 对象的内存布局

  - **对象头（Header)、实例数据（Instance Data)和对齐填充（Padding)。**

- 对象的访问定位

- 内存溢出与内存泄露

- 栈溢出（虚拟机栈和本地方法栈)

- 堆溢出

-  方法区溢出

- 直接内存溢出

- GC标记算法

  - 引用计数算法

    > 给每个对象添加一个计数器，当有地方引用该对象时计数器加1，当引用失效时计数器减1。用对象计数器是否为0来判断对象是否可被回收。缺点：无法解决循环引用的问题。

  - 可达性分析算法

    > - 通过GC ROOT的对象作为搜索起始点，通过引用向下搜索，所走过的路径称为引用链。通过对象是否有到达引用链的路径来判断对象是否可被回收（可作为GC ROOT的对象：虚拟机栈中引用的对象，方法区中类静态属性引用的对象，方法区中常量引用的对象，本地方法栈中JNI引用的对象）
    >
    > - 通过可达性算法，成功解决了引用计数所无法解决的循环依赖问题，只要你无法与GC Root建立直接或间接的连接，系统就会判定你为可回收对象。那这样就引申出了另一个问题，哪些属于GC Root。
    >
    >   - Java内存区域中可以作为GC ROOT的对象：
    >
    >     - 虚拟机栈中引用的对象
    >
    >     - 方法区中类静态属性引用的对象
    >
    >     - 方法区中常量引用的对象
    >
    >     - 本地方法栈中引用的对象

- GC回收算法

  - 标记-清除算法

  - 复制算法

  - 标记-整理算法

  - 分代收集算法

    > [https://share.note.youdao.com/ynoteshare/index.html?id=27f16d42fea30933884b640a941e8587&type=note&_time=1754630782894](https://share.note.youdao.com/ynoteshare/index.html?id=27f16d42fea30933884b640a941e8587&type=note&_time=1754630782894)

    

- Minor Full GC

  > [https://blog.csdn.net/u010796790/article/details/52213708](https://blog.csdn.net/u010796790/article/details/52213708)

- HotSpot的垃圾收集器

  - Serial垃圾收集器

  - ParNew垃圾收集器

  - .Parallel Scavenge收集器

  - Serial Old收集器

  #### 垃圾回收

  - [https://www.cnblogs.com/qian-fen/p/17341238.html](https://www.cnblogs.com/qian-fen/p/17341238.html)
  - [https://javaguide.cn/java/jvm/jvm-garbage-collection.html](https://javaguide.cn/java/jvm/jvm-garbage-collection.html)
  - [https://www.cnblogs.com/booksea/p/17665193.html](https://www.cnblogs.com/booksea/p/17665193.html)
  - [https://cloud.tencent.com/developer/article/1474779](https://cloud.tencent.com/developer/article/1474779) 线上full gc解决
  - [https://cloud.tencent.com/developer/article/2153851](https://cloud.tencent.com/developer/article/2153851)
  - G1收集器（重点)

- 内存分配原则

  - 对象优先在Eden分配

  - 大对象直接进入老年代

  - 长期存活的对象将进入老年代

  - 动态对象年龄判定

  - 空间分配担保

- GC相关API

  - System.gc

- 类加载机制

- 对象初始化的先后顺序

  

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

  - Jconsole 和Jstack可以查看死锁

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

- [https://pdai.tech/md/java/jvm/java-jvm-gc-zgc.html](https://pdai.tech/md/java/jvm/java-jvm-gc-zgc.html)

- [https://tech.meituan.com/2020/08/06/new-zgc-practice-in-meituan.html](https://tech.meituan.com/2020/08/06/new-zgc-practice-in-meituan.html)

java虚拟机工具

> - JPS 
>
>   - -l 输出主类全名
>
>   - -q 省略主类名称
>
>   - -m 输出主类传递给main函数的参数
>
>   - -V输出虚拟机启动时候的JVM参数
>
> - jstat -gc id 1000 20 （每隔一秒查询一次gc，然后输出20次）
>
>   ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250927-203940-8209378_cbd1c69b-5ca4-411d-d85b-840ab8eae112-20250927203940793.png)
>
>   - jinfo 查询配置信息
>
>   - jmap和jhat配合使用
>
>   - jhat一般不怎么用，相对简陋



#### 内存模型

- 背景

  - 传统计算机内存架构

    - CPU

      - CPU寄存器

      - CPU 高速缓存
        - L1,l2,l3缓存

      - 主存

  - 缓存一致性问题

    - 由于主存与 CPU 处理器的运算能力之间有数量级的差距，所以在传统计算机内存架构中会引入高速缓存来作为主存和处理器之间的缓冲，CPU 将常用的数据放在高速缓存中，运算结束后 CPU 再讲运算结果同步到主存中。使用高速缓存解决了 CPU 和主存速率不匹配的问题，但同时又引入另外一个新问题：缓存一致性问题。

    - 在多CPU的系统中(或者单CPU多核的系统)，每个CPU内核都有自己的高速缓存，它们共享同一主内存(Main Memory)。当多个CPU的运算任务都涉及同一块主内存区域时，CPU 会将数据读取到缓存中进行运算，这可能会导致各自的缓存数据不一致。

    - 因此需要每个 CPU 访问缓存时遵循一定的协议，在读写数据时根据协议进行操作，共同来维护缓存的一致性。这类协议有 MSI、MESI、MOSI、和 Dragon Protocol 等。

  - 处理器优化和指令重排序

    - 处理器优化其实也是重排序的一种类型，这里总结一下，重排序可以分为三种类型：

    - 编译器优化的重排序。编译器在不改变单线程程序语义放入前提下，可以重新安排语句的执行顺序。

    - 指令级并行的重排序。现代处理器采用了指令级并行技术来将多条指令重叠执行。如果不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序。

    - 内存系统的重排序。由于处理器使用缓存和读写缓冲区，这使得加载和存储操作看上去可能是在乱序执行。

  - 并发编程的问题
    - 熟悉 Java 并发的同学肯定对这三个问题很熟悉：『可见性问题』、『原子性问题』、『有序性问题』。如果从更深层次看这三个问题，其实就是上面讲的『缓存一致性』、『处理器优化』、『指令重排序』造成的。

- java内存模型

  - 限制处理器优化和使用内存屏障。

  - java 运行时候内存区域和硬件内存的关系

    - 了解过 JVM 的同学都知道，JVM 运行时内存区域是分片的，分为栈、堆等，其实这些都是 JVM 定义的逻辑概念。在传统的硬件内存架构中是没有栈和堆这种概念。

      ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250927-203946-8209378_57dcef8c-3a90-4ce4-f237-aa401eda1d2f.png)

  - Java 线程与主内存的关系

    - Java 内存模型是一种规范，定义了很多东西：

      - 所有的变量都存储在**主内存**（Main Memory）中。

      - 每个线程都有一个私有的**本地内存**（Local Memory），本地内存中存储了该线程以读/写共享变量的拷贝副本。

      - 线程对变量的所有操作都必须在本地内存中进行，而不能直接读写主内存。

      - 不同的线程之间无法直接访问对方本地内存中的变量。

        ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250927-203956-8209378_cdd6b62e-b91f-4eda-cb69-ff613fed2405.png)

    - 线程间通信

      - 为了更好的控制主存和本地内存的交互，Java 内存模型定义了八种操作来实现

        - lock：锁定。作用于主内存的变量，把一个变量标识为一条线程独占状态。

        - unlock：解锁。作用于主内存变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。

        - read：读取。作用于主内存变量，把一个变量值从主内存传输到线程的工作内存中，以便随后的load动作使用

        - load：载入。作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中。

        - use：使用。作用于工作内存的变量，把工作内存中的一个变量值传递给执行引擎，每当虚拟机遇到一个需要使用变量的值的字节码指令时将会执行这个操作。

        - assign：赋值。作用于工作内存的变量，它把一个从执行引擎接收到的值赋值给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。

        - store：存储。作用于工作内存的变量，把工作内存中的一个变量的值传送到主内存中，以便随后的write的操作。

        - write：写入。作用于主内存的变量，它把store操作从工作内存中一个变量的值传送到主内存的变量中。

  - 总结

    - 由于CPU 和主内存间存在数量级的速率差，想到了引入了多级高速缓存的传统硬件内存架构来解决，多级高速缓存作为 CPU 和主内间的缓冲提升了整体性能。解决了速率差的问题，却又带来了缓存一致性问题。

    - 数据同时存在于高速缓存和主内存中，如果不加以规范势必造成灾难，因此在传统机器上又抽象出了内存模型。

    - Java 语言在遵循内存模型的基础上推出了 JMM 规范，目的是解决由于多线程通过共享内存进行通信时，存在的本地内存数据不一致、编译器会对代码指令重排序、处理器会对代码乱序执行等带来的问题。

    - 为了更精准控制工作内存和主内存间的交互，JMM 还定义了八种操作：lock, unlock, read, load,use,assign, store, write。
