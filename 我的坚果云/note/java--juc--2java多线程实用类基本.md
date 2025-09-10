##### 线程的启动

- **继承thread**
- **实现Runnable接口**
-  **实现Callable接口**
- **fetureFask 接口**
  - 实现了Feture和runable

#### 线程状态

- synchronized

- Lock

- volatile

- Atomic

- #### Lock使用 深入

  - 可重入锁 ReentrantLock
  - Condition（与wait&notify区别）
    - BlockingQueue就是基于Condition实现的。
  - Condition与wait&notify区别
  - await&signal

- #### 读写锁 ReentrantReadWriteLock

- #### LockSupport（锁住的是线程，synchronized锁住的是对象）

- #### synchronized与Lock的区别

  - 层次：前者是JVM实现，后者是JDK实现
  - 功能：前者仅能实现互斥与重入，后者可以实现 可中断、可轮询、可定时、可公平、绑定多个条件、非块结构synchronized在阻塞时不会响应中断，Lock会响应中断，并抛出InterruptedException异常。
  - 异常：前者线程中抛出异常时JVM会自动释放锁，后者必须手工释放
  - **性能：synchronized性能已经大幅优化，如果synchronized能够满足需求，则尽量使用synchronized**

- #### AtomicStampedReference

- #### Java内存模型 线程同步工具原理

  - 指令重排java序
  - 内存屏障
  - happens-before（抽象概念，基于内存屏障）

- #### synchronized原理

  - 代码块同步是使用monitorenter和monitorexit指令实现
  - 锁的分类
    - 无锁
    - 偏向锁（只有一个线程进入临界区）
    - 轻量级锁（多个线程交替进入临界区）
    - 重量级锁（多个线程同时进入临界区）

- #### 原子操作原理

  - CPU如何实现原子操作
    - 1）在硬件层面，CPU依靠总线加锁和缓存锁定机制来实现原子操作。
  - Java如何实现原子操作
    - 2）Java使用了锁和循环CAS的方式来实现原子操作。

- #### 同步容器

  - ConcurrentHashMap
  - CopyOnWriteArrayList
    - 显然，每当修改容器时都会复制底层数组，这需要一定的开销，特别是当容器的规模较大时，仅当迭代操作远远多于修改操作时，才应该使用写入时复制容器。
  - BlockingQueue
  - ThreadLocal

- #### 同步工具使用

  - Semaphore（信号量）
  - CyclicBarrier（可循环使用的屏障/栅栏）
  - Exchanger（两个线程交换数据）
  - CountDownLatch（闭锁）
  - FutureTask（Future实现类）
    - FurureTask是Future接口的唯一实现类。
  - Future
    - Future接口设计初衷是对将来某个时刻会发生的结果进行建模。它建模了一种异步计算，返回一个执行运算结果的引用，当运算结束后，这个引用被返回给调用方。 在Future中触发那些潜在耗时的操作把调用线程解放出来，让它能继续执行其他有价值的工作，不再需要等待耗时的操作完成。

- #### CompletableFuture

  - supplyAsync 提交任务
  - thenApply 变换（等待前一个任务返回后执行，处于同一个CompletableFuture）
  - thenAccept 消耗
  - thenRun 执行下一步操作，不关心上一步结果 
  - thenCombine 结合两个CompletionStage的结果，进行转化后返回 
  - thenCompose（合并多个CompletableFuture，流水线执行，在调用外部接口返回CompletableFuture类型时更方便）
  - thenAccptBoth 结合两个CompletionStage的结果，进行消耗 
  - runAfterBoth 在两个CompletionStage都运行完执行，不关心上一步结果
  - applyToEither 两个CompletionStage，谁计算的快，我就用那个CompletionStage的结果进行下一步的转化操作 
  - acceptEither 两个CompletionStage，谁计算的快，我就用那个CompletionStage的结果进行下一步的消耗操作 
  - runAfterEither 两个CompletionStage，任何一个完成了都会执行下一步的操作，不关心上一步结果
  - exceptionally 当运行时出现了异常，可以进行补偿
  - whenComplete 当运行完成时，若有异常则改变返回值，否则返回原值
  - handle 当运行完成时，无论有无异常均可转换
  - allOf
    - allOf工厂方法接收一个由CompletableFuture构成的数组，数组中的所有CompletableFuture对象执行完成之后，它返回一个CompletableFuture<Void>对象。这意味着，如果你需要等待最初Stream中的所有CompletableFuture对象执行完毕，对allOf方法返回的CompletableFuture执行join操作是个不错的注意。
  - anyOf
    - 只要CompletableFuture对象数组中有一个执行完毕，便不再等待。

- #### ForkJoin

  - Fork/Join使用两个类来完成以上两件事情：

    - RecursiveAction：用于没有返回结果的任务。

    - RecursiveTask ：用于有返回结果的任务。

  - 原理浅析

    - 每个Worker线程都维护一个任务队列，即ForkJoinWorkerThread中的任务队列。

    - 任务队列是双向队列，这样可以同时实现LIFO和FIFO。

    - 子任务会被加入到原先任务所在Worker线程的任务队列。

    - Worker线程用LIFO的方法取出任务，也就后进队列的任务先取出来（子任务总是后加入队列，但是需要先执行）。

    - Worker线程的任务队列为空，会随机从其他的线程的任务队列中拿走一个任务执行（所谓偷任务：steal work，FIFO的方式）。

    - 如果一个Worker线程遇到了join操作，而这时候正在处理其他任务，会等到这个任务结束。否则直接返回。

    - 如果一个Worker线程偷任务失败，它会用yield或者sleep之类的方法休息一会儿，再尝试偷任务（如果所有线程都是空闲状态，即没有任务运行，那么该线程也会进入阻塞状态等待新任务的到来）。

  - 与MapReduce的区别

    - MapReduce是把大数据集切分成小数据集，并行分布计算后再合并。

    - ForkJoin是将一个问题递归分解成子问题，再将子问题并行运算后合并结果。

    - 二者共同点：都是用于执行并行任务的。基本思想都是把问题分解为一个个子问题分别计算，再合并结果。应该说并行计算都是这种思想，彼此独立的或可分解的。从名字上看Fork和Map都有切分的意思，Join和Reduce都有合并的意思，比较类似。

- #### 线程池使用

  - 线程池动态变化

    - 小于核心线程数，新的任务会被创建

    - 一旦等于核心线程数，就会被放入队列

    - 队列满了，并且coresize小于maxSize，创建临时线程

    - 当提交任务数超过maximumPoolSize时,走拒绝策略

    - 当线程池中超过corePoolSize线程，空闲时间达到keepAliveTime时，关闭空闲线程 

    - 当设置allowCoreThreadTimeOut(true)时，线程池中corePoolSize线程空闲时间达到keepAliveTime也将关闭

  - 三种队列区别https://blog.csdn.net/qq_26881739/article/details/80983495

  - 使用注意

    - 1.只有当任务都是同类型并且相互独立时，线程池的性能才能达到最佳。如果将运行时间较长的与运行时间较短的任务混合在一起，那么除非线程池很大，否则将可能造成拥塞。如果提交的任务依赖于其他任务，那么除非线程池无限大，否则将可能造成死锁。幸运的是，在基于网络的典型服务器应用程序中——web服务器、邮件服务器、文件服务器等，它们的请求通常都是同类型的并且相互独立的。

    - 2、设置线程池的大小：

      - 基于Runtime.getRuntime().avialableprocessors() 进行动态计算，（其实只要大于内核，只要不是特别过分，一般都是可以的，因为线程的上下文切换通常很短，几微妙而已。没必要太刻意）

    - 3、线程的创建与销毁

    - 4、管理队列任务

    - 5、饱和策略

      - 中止策略是默认的饱和策略

      - 新提交的任务无法保存到队列中执行时，抛弃策略会悄悄抛弃该任务

  - #### 扩展ThreadPoolExecutor

  - #### 任务时限

    - Future的get方法可以限时，如果超时会抛出TimeOutException，那么此时可以通过cancel方法来取消任务。如果编写的任务是可取消的，那么可以提前中止它，以免消耗过多的资源。

  - #### shutdown和shutdownNow区别任务关闭

    - 1）它们都会阻止新任务的提交

    - 2）正常关闭是停止空闲线程，正在执行的任务继续执行并完成所有未执行的任务

    - 3）强行关闭是停止所有（空闲+工作）线程，关闭当前正在执行的任务，然后返回所有尚未执行的任务。

  - #### ScheduledThreadPoolExecutor

  - #### Executors

    - FixedThreadPool

    - SingleThreadExecutor

    - CachedThreadPool

    - ScheduledThreadPoolExecutor

  - #### CompletionService

    - CompletionService将Executor和BlockingQueue的概念融合在一起，你可以将Callable任务提交给它来执行，然后使用类似于队列操作的take和poll等方法来获得已完成的结果，而这些结果会在完成时封装为Future。ExecutorCompletionService实现了CompletionService并将计算任务委托给一个Executor。

  - #### J.U.C 源码解析

    - AQS实现原理
      - AQS依赖内部的CLH同步队列（一个FIFO双向队列）来完成同步状态的管理。当前线程获取同步状态失败时，AQS会将当前线程以及等待状态等信息构造为一个Node并将其加入同步队列，并阻塞当前线程。当同步状态释放时，会把后继节点线程唤醒，使其再次尝试获取同步状态。后继节点将会在获取同步状态成功时将自己设置为头节点。

#### 

​	