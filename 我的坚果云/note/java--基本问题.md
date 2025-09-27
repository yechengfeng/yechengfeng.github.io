- concurrent and parallel 区别

  - [视频翻译--7. differences between concurrency and parallelism](https://mubu.com/doc7g2gYBivQg0) 

- HashMap原理

  - \1. [为什么hashmap(Java8)的底层实现是使用红黑树而不是跳表?](https://www.zhihu.com/question/344666825/answer/1905309511989825969)

- Java--String 从 char 数组改为 byte 数组的原因 

  - [https://javabetter.cn/basic-extra-meal/jdk9-char-byte-string.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E4%BC%98%E5%8C%96](https://javabetter.cn/basic-extra-meal/jdk9-char-byte-string.html#为什么要优化)

- 基本数据类型

  - byte 1 short 2 int 4 long 8

  - float 4 double 8

  - char 2

  - boolean 1

- 包装类型

  - 基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。

  - 在 Java 8 中，Integer 缓存池的大小默认为 -128~127。编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

- String

  - String 被声明为 final，因此它不可被继承。在 Java 8 中，String 内部使用 char 数组存储数据。 

  - 不可变的好处

    -  可以缓存 hash 值

    - 因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

    -  String Pool 的需要

    - 如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。

- String, StringBuffer and StringBuilder

  - StringBuffer 是线程安全的，内部使用 synchronized 进行同步（StringBuffer几乎不用了，笨重，场景很少，用不到跨线程共享）

  - java9之后甚至更推荐使用String 拼接，虚拟机会做优化处理

- 反射

  - Java 反射机制可以动态地创建对象并调用其属性，这样的对象的类型在编译期是未知的

- 异常

  - Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： Error 和 Exception。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：

    - 受检异常 ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；

    - 非受检异常 ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

- 泛型

  - 类型的参数化，就是可以把类型像方法的参数那样传递。泛型使编译器可以在编译期间对类型进行检查以提高类型安全，减少运行时由于对象类型不匹配引发的异常

- Object的通用方法

  - \1. hashCode

    - hashCode() 返回散列值，而 equals() 是用来判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价。

    - 在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象散列值也相等。

  - \2. equals

  - \3. clone

    - 应该注意的是，clone() 方法并不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。Cloneable 接口只是规定，如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛出 CloneNotSupportedException。

    - 使用 clone() 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。Effective Java 书上讲到，最好不要去使用 clone()，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。

  - \4. toString

  - \5. getClass

  - \6. finalize

  - \7. notify

  - \8. notifyAll

  - \9. wait

  - \10. waitAll

- 集合

  - Collection包含3个分支

    - AbstractCollection是抽象类，实现了部分Collection中的API，如contains，toArray, remove, toString等方法。

      - Queue
        - 队列是一种特殊的线性表，允许在表的头部进行删除操作，在表的尾部进行插入操作。有2个继承接口，BlockingQueue(阻塞队列)和Deque(双向队列)。AbstractQueue是抽象类，实现了Queue中的大部分API，常见实现类有LinkedQueue。

      - List
        - List是一个有序的队列，每个元素都有它的索引，第一个元素的索引值为0。AbstractList是抽象类，实现了List中的大部分API，常见实现类有 Stack。ArrayList、LinkedList、Stack 、Vector以及CopyOnWriteArrayList

      - Set
        - Set是一个不允许有重复元素的集合。 AbstractSet是抽象类，实现了Set中的大部分API，常见的实现类有HashSet、TreeSet、LinkedHashSet

  - Map包含1个分支

    - Map是一个映射接口，即key-value的键值对。AbstractMap是抽象类，实现了Map中的大部分API，HashMap, TreeMap, WeakHashMap是其实现类。

    - HashMap、TreeMap、LinkedHashMap、Hashtable、ConcurrentHashMap 以及 Properties ,WeakHashMap等；

- Concurrent包下的集合概述

  - Concurrent主要有3个package组成

    - java.util.concurrent
      - 提供大部分关于并发的接口和类，如BlockingQueue, ConcurrentHashMap, ExecutorService等

    - java.util.concurrent.atomic
      - 提供所有的原子类操作，如AtomicInteger, AtomicLong等

    - java.util.concurrent.locks
      - 提供锁相关的类，如Lock, ReentrantLock, ReadWriteLock, Condition等

- java创建类的四种方式

  - 1.使用new创建对象
    - 使用new关键字创建对象应该是最常见的一种方式，但我们应该知道，使用new创建对象会增加耦合度。无论使用什么框架，都要减少new的使用以降低耦合度。

  - 2.使用反射的机制创建
    - 对象使用Class类的newInstance方法

  - 3.采用clone
    - 需要已经有一个分配了内存的源对象，创建新对象时，首先应该分配一个和源对象一样大的内存空间。要调用clone方法需要实现Cloneable接口

  - 4.采用序列化机制

- 字节流与字符流区别使用场景

  - https://www.cnblogs.com/geekbruce/articles/18993288

  - https://zhuanlan.zhihu.com/p/260191541

- Math.round(11.5)等於多少? Math.round(-11.5)等於多少?

  - Math 类中提供了三个与取整有关的方法：ceil、floor、round，这些方法的作用与它们的英文名称的含义相对应，例如，ceil 的英文意义是天花板，该方法就表示向上取整，Math.ceil(11.3)的结果为12,Math.ceil(-11.3)的结果是-11；floor 的英文意义是地板，该方法就表示向下取整，Math.ceil(11.6)的结果为11,Math.ceil(-11.6)的结果是-12；最难掌握的是round 方法，它表示“四舍五入”，算法为 Math.floor(x+0.5)，即将原来的数字加上0.5后再向下取整，所以，Math.round(11.5)的结果为12，Math.round(-11.5)的结果为-11。

- 静态代码块和非静态代码块区别

  - 静态[代码](http://www.so.com/s?q=代码&ie=utf-8&src=internal_wenda_recommend_textn)块，在[虚拟机](http://www.so.com/s?q=虚拟机&ie=utf-8&src=internal_wenda_recommend_textn)加载类的时候就会加载执行，而且只执行一次;

  - 非静态代码块，在创建对象的时候(即new一个对象的时候)执行，每次创建对象都会执行一次

- JDK&JRE&JVM区别

  - [https://cloud.tencent.com/developer/article/1892442](https://cloud.tencent.com/developer/article/1892442)

- 基础数据类型包装类

  - [https://cloud.tencent.com/developer/article/1579086](https://cloud.tencent.com/developer/article/1579086)
    - 为什么需要

- ThreadLocal（线程局部变量)

  - 每个线程的变量副本是存储在哪里的
    - ThreadLocal的get方法就是从当前线程的ThreadLocalMap中取出当前线程对应的变量的副本。该Map的key是ThreadLocal对象，value是当前线程对应的变量。

  - 为什么ThreadLocalMap的Key是弱引用 [java--四种引用类型](https://mubu.com/doc1ZFlRHdxuM0) 

    - 如果是强引用，ThreadLocal将无法被释放内存。

    - 因为如果这里使用普通的key-value形式来定义存储结构，实质上就会造成节点的生命周期与线程强绑定，只要线程没有销毁，那么节点在GC分析中一直处于可达状态，没办法被回收，而程序本身也无法判断是否可以清理节点。弱引用是Java中四档引用的第三档，比软引用更加弱一些，如果一个对象没有强引用链可达，那么一般活不过下一次GC。当某个ThreadLocal已经没有强引用可达，则随着它被垃圾回收，在ThreadLocalMap里对应的Entry的键值会失效，这为ThreadLocalMap本身的垃圾清理提供了便利。

    - JDK建议将ThreadLocal变量定义成private static的，这样的话ThreadLocal的生命周期就更长，由于一直存在ThreadLocal的强引用，所以ThreadLocal也就不会被回收，也就能保证任何时候都能根据ThreadLocal的弱引用访问到Entry的value值，然后remove它，防止内存泄露。

- Iterator / ListIterator / Iterable

  - 普通for循环时不能删除元素，否则会抛出异常；Iterator可以。

- Comparable与Comparator

  - 基本数据类型包装类和String类均已实现了Comparable接口。

  - 实现了Comparable接口的类的对象的列表或数组可以通过Collections.sort或Arrays.sort进行自动排序，默认为升序。

  - 可以将 Comparator 传递给 sort 方法（如 Collections.sort 或 Arrays.sort)，从而允许在排序顺序上实现精确控制。还可以使用 Comparator 来控制某些数据结构（如TreeSet,TreeMap)的顺序。

- try-finally-return

  - 1.当 try 代码块和 catch 代码块中有 return 语句时，finally 仍然会被执行。

  - 2.执行 try 代码块或 catch 代码块中的 return 语句之前，都会先执行 finally 语句。

  - 3.无论在 finally 代码块中是否修改返回值，返回值都不会改变，仍然是执行 finally 代码块之前的值。

  - \4. finally 代码块中的 return 语句一定会执行。

- static作用

  - 1)修饰成员方法：静态成员方法

    - 在静态方法中不能访问类的非静态成员变量和非静态成员方法；

    - 在非静态成员方法中是可以访问静态成员方法/变量的；

    - 即使没有显式地声明为static，类的构造器实际上也是静态方法

  - 2)修饰成员变量：静态成员变量

    - 静态变量和非静态变量的区别是：静态变量被所有的对象所共享，在内存中只有一个副本，它当且仅当在类初次加载时会被初始化。而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响。

    - 静态成员变量并发下不是线程安全的，并且对象是单例的情况下，非静态成员变量也不是线程安全的。

    - 怎么保证变量的线程安全?

    - 只有一个线程写，其他线程都是读的时候，加volatile；线程既读又写，可以考虑Atomic原子类和线程安全的集合类；或者考虑ThreadLocal

  - 3)修饰代码块：静态代码块
    - 用来构造静态代码块以优化程序性能。static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。

  - 4)修饰内部类：静态内部类

    - 成员内部类和静态内部类的区别：

      - 1)前者只能拥有非静态成员；后者既可拥有静态成员，又可拥有非静态成员

      - 2)前者持有外部类的的引用，可以访问外部类的静态成员和非静态成员；后者不持有外部类的引用，只能访问外部类的静态成员

      - 3)前者不能脱离外部类而存在；后者可以

  - 5)修饰import：静态导包
    - [https://juejin.cn/post/6955114797994082318](https://juejin.cn/post/6955114797994082318)

- 异常

  - Error、Exception

    - Error是程序无法处理的错误，它是由JVM产生和抛出的，比如OutOfMemoryError、ThreadDeath等。这些异常发生时，Java虚拟机（JVM)一般会选择线程终止。

    - Exception是程序本身可以处理的异常，这种异常分两大类运行时异常和非运行时异常。程序中应当尽可能去处理这些异常。

  - 常见RuntimeException	

    - IllegalArgumentException - 方法的参数无效

    - NullPointerException - 试图访问一空对象的变量、方法或空数组的元素

    - ArrayIndexOutOfBoundsException - 数组越界访问

    - ClassCastException - 类型转换异常

    - NumberFormatException 继承IllegalArgumentException，字符串转换为数字时出现。比如int i= Integer.parseInt("ab3");

  - RuntimeException与非Runtime	Exception

    - RuntimeException是运行时异常，也称为未检查异常；

    - 非RuntimeException 也称为CheckedException 受检异常

    - 前者可以不必进行try-catch，后者必须要进行try-catch或者throw。

  - 异常包装

    - 当捕获到异常时，可以使用getCause方法来重新得到原始异常，Throwable e = se.getCause(); 建议使用这种包装技术，可以抛出系统的高级异常（自己new的)，又不会丢失原始异常的细节。

      - try{

      - …

      - }catch(SQLException e){

      - Throwable se =  new ServletException(e.getMessage());

      - se.initCause(e);

      - throw se;

      - }

  - IO

    - 一共有5种io模型，java nio使用的时多路复用模型。

      - 阻塞io：用户进程阻塞，知道io就绪且io处理（读写）完成，阻塞才解除。

      - 非阻塞IO：用户进程不阻塞，需要不断轮询io是否就绪，很占用cpu资源，所以一般不用

      - 多路复用IO：用户进程阻塞，也是轮询io是否就绪，但是和非阻塞IO不一样的是，轮询io的是系统进程而非应用进程，实际使用了代理（select/poll/epoll），可以避免cpu空转，所以效率较高，而且1个进程可以管理多个socket，java nio使用的就是多路复用模型。

      - 信号驱动式 I/O：（有点类似回调）用户进程不阻塞，而是安装一个信号处理函数，当数据准备好时，进程会收到一个 SIGIO 信号，可以在信号处理函数中调用 I/O 操作函数处理数据。

      - 异步io：用户进程不阻塞，发起io请求（读写）后就可以做自己的事了，io就绪等待和io操作交给操作系统去完成，完成后调用用户进程处理函数，所以整个过程都是异步的。

    - [https://segmentfault.com/a/1190000006824196](https://segmentfault.com/a/1190000006824196)

    - [https://blog.csdn.net/u013063153/article/details/76473578](https://blog.csdn.net/u013063153/article/details/76473578)

    - [https://www.jianshu.com/p/052035037297](https://www.jianshu.com/p/052035037297)

    - Unix IO模型

      - 异步I/O 是指用户程序发起IO请求后，不等待数据，同时操作系统内核负责I/O操作把数据从内核拷贝到用户程序的缓冲区后通知应用程序。数据拷贝是由操作系统内核完成，用户程序从一开始就没有等待数据，发起请求后不参与任何IO操作，等内核通知完成。

      -  同步I/O 就是非异步IO的情况，也就是用户程序要参与把数据拷贝到程序缓冲区（例如java的InputStream读字节流过程

      - 同步IO非阻塞 是指用户程序发起IO操作请求后不等待数据，而是调用会立即返回一个标志信息告知条件不满足，数据未准备好，从而用户请求程序继续执行其它任务。执行完其它任务，用户程序会主动轮询查看IO操作条件是否满足，如果满足，则用户程序亲自参与拷贝数据动作。
        - Unix IO模型的语境下，同步和异步的区别在于数据拷贝阶段是否需要完全由操作系统处理。阻塞和非阻塞操作是针对发起IO请求操作后是否有立刻返回一个标志信息而不让请求线程等待。

    - BIO NIO AIO介绍

      - BIO：同步阻塞，每个客户端的连接会对应服务器的一个线程

      - NIO：同步非阻塞，多路复用器轮询客户端的请求，每个客户端的IO请求会对应服务器的一个线程

      - AIO： 异步非阻塞，客户端的IO请求由OS完成后再通知服务器启动线程处理（需要OS支持)

        - 1、进程向操作系统请求数据 

        - 2、操作系统把外部数据加载到内核的缓冲区中， 

        - 3、操作系统把内核的缓冲区拷贝到进程的缓冲区 

        - 4、进程获得数据完成自己的功能

      - Java NIO属于同步非阻塞IO，即IO多路复用，单个线程可以支持多个IO

        - 即询问时从IO没有完毕时直接阻塞，变成了立即返回一个是否完成IO的信号。

        - 异步IO就是指AIO，AIO需要操作系统支持。

- java 泛型和泛型擦除 

  - [https://blog.csdn.net/briblue/article/details/76736356](https://blog.csdn.net/briblue/article/details/76736356)

  - 目的：增强类型安全性，减少运行时错误，避免强制类型转换，提高代码复用性。

  - 核心概念

    - 类型参数化：在类、接口或方法中定义类型参数（如 <T>）。

    - 类型安全：编译时检查类型一致性，避免 ClassCastException。

    - 代码复用：一套逻辑处理多种数据类型（如 List<T>）。

  - 泛型擦除（Type Erasure）
    - Java 泛型在编译后类型信息被移除，替换为原生类型（Raw Type）或边界类型，以确保与旧版本 JVM 兼容。

- synchronized可以锁字符串吗

  - [https://www.cnblogs.com/xrq730/p/6662232.html](https://www.cnblogs.com/xrq730/p/6662232.html) 锁字符串的生产事故（一定要注意是否是同一对象，而且最好全局唯一）

  - 通常是可以锁字符串的，但是锁字符串会遇到问题，比如如果是以http接口的形式传递string，那么传递的是一个对象，不是同一个string对象，那么达不到加锁的目的，解决方法可以用 String的intern()方法，但是还有一个问题，这样的话，如果不是全局唯一 ，职责单一的 String,可能导致同一个接口使用同一个锁，导致接口互斥。影响性能

- sleep() 和 wait() 有什么区别?

  - sleep是线程类（Thread）的方法，导致此线程暂停执行指定时间，给执行机会给其他线程，但是监控状态依然保持，到时后会自动恢复。调用sleep不会释放对象锁。

  - wait是Object类的方法，对此对象调用wait方法导致本线程放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象发出notify方法（或not ifyAll）后本线程才进入对象锁定池准备获得对象锁进入运行状态。

- HttpSession

  - [https://blog.csdn.net/zy2317878/article/details/80275463](https://blog.csdn.net/zy2317878/article/details/80275463)

- 常见连接池

  -  [https://blog.csdn.net/qq_34359363/article/details/72763491](https://blog.csdn.net/qq_34359363/article/details/72763491)

- 类加载器 (*)重要

  - 启动类加载器(Bootstrap ClassLoader)：负责加载 JAVA_HOME\lib目录中的，或通过-Xbootclasspath参数指定路径中的，且被虚拟机认可（按文件名识别，如rt.jar）的类。

  - 扩展类加载器(Extension ClassLoader)：负责加载 JAVA_HOME\lib\ext目录中的，或通过java.ext.dirs系统变量指定路径中的类库。

  - 应用程序类加载器(Application ClassLoader)：负责加载用户路径（classpath）上的类库。

  - JVM通过双亲委派模型进行类的加载，当然我们也可以通过继承java.lang.ClassLoader实现自定义的类加载器。

  - 当一个类加载器收到类加载任务，会先交给其父类加载器去完成，因此最终加载任务都会传递到顶层的启动类加载器，只有当父类加载器无法完成加载任务时，才会尝试执行加载任务。

  - 采用双亲委派的一个好处是比如加载位于rt.jar包中的类java.lang.Object，不管是哪个加载器加载这个类，最终都是委托给顶层的启动类加载器进行加载，这样就保证了使用不同的类加载器最终得到的都是同样一个Object对象。

- String底层(*)

  - [https://blog.csdn.net/yadicoco49/article/details/77627302](https://blog.csdn.net/yadicoco49/article/details/77627302)

- 拦截器和过滤器区别

  - [https://www.cnblogs.com/vipstone/p/16796867.html](https://www.cnblogs.com/vipstone/p/16796867.html)

- OOM, Out of Memory 错误

  - 常见OOM情况

    - Java堆内存溢出，此种情况最常见，一般由于内存泄露或者堆的大小设置不当引起。对于内存泄露，需要通过内存监控软件查找程序中的泄露代码，而堆大小可以通过虚拟机参数-Xms,-Xmx等修改。

    - Java永久代溢出，即方法区溢出了，一般出现于大量Class或者jsp页面，或者采用cglib等反射机制的情况，因为上述情况会产生大量的Class信息存储于方法区。此种情况可以通过更改方法区的大小来解决，使用类似-XX:PermSize=64m -XX:MaxPermSize=256m的形式修改。另外，过多的常量尤其是字符串也会导致方法区溢出。

    - java.lang.StackOverflowError, 不会抛OOM error，但也是比较常见的Java内存溢出。JAVA虚拟机栈溢出，一般是由于程序中存在死循环或者深度递归调用造成的，栈大小设置太小也会出现此种溢出。可以通过虚拟机参数-Xss来设置栈的大小。

    - 静态集合类

- 双亲委派 [https://zhuanlan.zhihu.com/p/705120533](https://zhuanlan.zhihu.com/p/705120533)

死锁OOM,CPU高并发之类的问题

> - 百万数据导出excel ,
>
>   - 解决OOM问题.利用多线程
>
> - 线上OOM 排查
>
>   - 一次性申请的太多 （更改申请对象的数量）
>
>     - 提前设置参数，如果OOM,导出dump 文件
>
>     - Arthas 分析
>
>   - 内存资源耗尽未释放 （找到未释放的对象进行释放）
>
>   -  本身资源不够 （jmap -heap 查看堆信息）
>
> - 线上CPU飙升
>
>   - TOP找到 PID 
>
>   - 根据 TOP -H -p +PID 找到线程ID
>
>   - 将线程ID转换成16进制 ，printf '0x%x\n' pid
>
>   - jstack (jstack pid |grep 16进制线程PID -A 20)
>
> - 线程死锁
>
>   - jstack +PID 如果有死锁，就会 found 1 deadlock
>
>   - 产生死锁四大因素
>
>     - 互斥 共享资源只能被一个线程占用 -互斥锁
>
>     - 占有且等待 （利用一个资源管理者，一个线程一次性占有多个资源，用完一起释放）
>
>     - 不可抢占 tryLock 设置锁超时时间
>
>     - 循环等待 : T1等待线程T2, 线程T2等待T1 . 所以锁的时候按照顺序去锁，ID 大的先锁，小的后锁，就不会出现死锁 
>
> - 秒杀系统设计
>
>   - 痛点
>
>     - 瞬时并发量大
>       - 大量用户同一时间抢购，网站的瞬时流量激增 
>
>     - 库存少
>
>   - 设计
>
>     - 访问层 -商品页
>
>       -  将图片,css,js上传到cdn,解决前端服务器压力
>
>       - 秒杀按钮，活动前禁用，点击后禁用按钮，滑动验证码防止羊毛党
>
>     - 中间转发层
>
>       -  单台nginx 处理俩三万并发请求， 可以使用 集群，配合硬件级别的负载均衡器（F5/LVS），Nginx 配置限流
>
>       - docker/k8s 云服务器的动态伸缩部署
>
>       - MQ 削峰
>
>     - 预热 redis 做缓存 ，可以将商品预热到redis 中
>
>     - 库存 lua 脚本保证原子性
>
>     - 防止重复 redis setNx 针对IP,用户ID
>
>     - 分布式锁
>
>     - 异步处理库存
>
>     - 读写分离