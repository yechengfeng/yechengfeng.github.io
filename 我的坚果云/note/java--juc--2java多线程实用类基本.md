##### 线程的启动

- **继承thread**
- **实现Runnable接口**
-  **实现Callable接口**
- **fetureFask 接口**
  - 实现了Feture和runable

#### 线程状态

> - **NEW**: The thread has been created and it has not yet started
> - **RUNNABLE**: The thread is being executed in the JVM
> - **BLOCKED**: The thread is blocked and it is waiting for a monitorWAITING: The thread is waiting for another thread
> - **TIMED_WAITING**: The thread is waiting for another thread with a specified waiting time
> - **TERMINATED**: The thread has finished its execution

- Lock

- volatile

- Atomic

  - 原子类是 java.util.concurrent 包的核心部分之一，它们通过 无锁（Lock-Free） 机制提供了线程安全的原子操作。原子类使用底层的 CAS（Compare-And-Swap） 操作，避免了传统的锁机制所带来的开销和性能瓶颈。常见的原子类包括：

    - AtomicBoolean：原子地更新 boolean 值

    - AtomicInteger：原子地更新 int 值

    - AtomicLong：原子地更新 long 值

    - AtomicReference：原子地更新引用类型

    - AtomicIntegerArray/ AtomicLongArray /AtomicReferenceArray：原子地更新数组中的元素

  - 所有的原子类本质上都依赖于底层的 CAS 操作 来实现无锁并发。CAS 的核心思想是通过硬件指令比较内存中的值是否等于预期值，如果相等则将其更新为新值，否则不更新，整个过程是原子的。

  - **实现原理**

    - 什么是CAS （Compare-And-Swap）它是一条 CPU 并发原语

      - CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A，计算后要修改后的新值B。

        - （1）初始状态：在内存地址V中存储着变量值为 1。

        -  （2）线程1想要把内存地址为 V 的变量值增加1。这个时候对线程1来说，旧的预期值A=1，要修改的新值B=2。

        - （3）在线程1要提交更新之前，线程2捷足先登了，已经把内存地址V中的变量值率先更新成了2。

        - （4）线程1开始提交更新，首先将预期值A和内存地址V的实际值比较（Compare），发现A不等于V的实际值，提交失败。

        - （5）线程1重新获取内存地址 V 的当前值，并重新计算想要修改的新值。此时对线程1来说，A=2，B=3。这个重新尝试的过程被称为自旋。如果多次失败会有多次自旋。

        - （6）线程 1 再次提交更新，这一次没有其他线程改变地址 V 的值。线程1进行Compare，发现预期值 A 和内存地址 V的实际值是相等的，进行 Swap 操作，将内存地址 V 的实际值修改为 B。

        - 总结：更新一个变量的时候，只有当变量的预期值 A 和内存地址 V 中的实际值相同时，才会将内存地址 V 对应的值修改为 B，这整个操作就是CAS。

      - CAS原理

        - CAS 是一种系统原语，原语属于操作系统用语，原语由若干指令组成，用于完成某个功能的一个过程，并且原语的执行必须是连续的，在执行过程中不允许被中断，也就是说 CAS 是一条 CPU 的原子指令，由操作系统硬件来保证。

      -  CAS引发 的问题

        - **典型 ABA 问题**

          - ABA 是 CAS 操作的一个经典问题，假设有一个变量初始值为 A，修改为 B，然后又修改为 A，这个变量实际被修改过了，但是 CAS 操作可能无法感知到。

          - 如果是整形还好，不会影响最终结果，但如果是对象的引用类型包含了多个变量，引用没有变实际上包含的变量已经被修改，这就会造成大问题。

          - 如何解决？思路其实很简单，在变量前加版本号，每次变量更新了就把版本号加一
            - 从 JDK 1.5 开始提供了AtomicStampedReference类，这个类的 compareAndSe 方法首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志`，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值。

        - **自旋开销问题**

          - CAS 出现冲突后就会开始自旋操作，如果资源竞争非常激烈，自旋长时间不能成功就会给 CPU 带来非常大的开销。

          - 解决方案：可以考虑限制自旋的次数，避免过度消耗 CPU；另外还可以考虑延迟执行。

        - **只能保证单个变量的原子性**

- #### Lock使用深入

  - 可重入锁 ReentrantLock

    - 有几个功能

      1. 中断响应
         - `reentrantLock.lockInterruptibly(); // 线程在等待锁的过程中，可以响应中断`
      2. 锁申请等待限时
         - `lock.tryLock (5,TimeUnit.SECONDS) //可以解决死锁问题`
      3. 公平锁
         - 如果我们使用synchronized 关键字进行锁控制，那么产生的锁就是非 公平的。ReentrantIock(boolean fair)，可以设置公平非公平。公平锁看起来很优美，但是要实现公平锁必然 要求系统维护一个有序队列，因此公平锁的实现成本比较高，性能却非常低下，因此，在默认情况下，锁是非公平的。如果没有特别的需求，则不需要使用公平锁

    - 核心字段和方法

      > - private final Sync sync：ReentrantLock 通过 Sync 类来实现锁的逻辑。Sync 是一个抽象类，有两个子类 FairSync 和 NonFairSync，分别实现公平锁和非公平锁。
      > - lock()：加锁方法。对于非公平锁，NonfairSync 的 lock() 方法先尝试通过 compareAndSetState 尝试抢占锁，如果抢不到锁，则调用 acquire() 进入同步队列等待。
      > - unlock()：解锁方法。调用 release() 方法来释放锁，内部通过 CAS 修改锁状态并唤醒等待队列中的线程。

    - 公平锁与非公平锁的区别：

      > - 公平锁：新线程会按照先后顺序竞争锁，依次进入队列排队。
      >
      > - 非公平锁：线程可以直接尝试抢占锁，不论顺序，抢不到才进入队列排队。

  - Condition（与wait&notify区别,一个是Object提供的,和sync配合，一个是和重入锁关联）

    - BlockingQueue就是基于Condition实现的。

    - `void awaitUninterruptibly() \\等待过程中不会响应中断`

    - `awaitUntil(Date date) `

    - API 

      ```java
      public void acquire ()
      public void acquireUninterruptibly ()
       public boolean tryAcquire ()
      public boolean tryAcquire (long timeout, TimeUnit unit)
       public void release ()
      ```

  - await&signal

- #### 读写锁 ReentrantReadWriteLock

- #### LockSupport（锁住的是线程，synchronized锁住的是对象）

  - > LockSuport 是一个非常方便实用的线程阻塞工具，它可以在线程内任意位置让线程阻塞。 与Thread. suspen()方法相比，它弥补了由于resume() 方法发生导致线程无法继续执行的情况。 和Object.wait()方法相比，它不需要先获得某个对象的锁，也不会抛出InterruptedExcept ion 异常

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

  - **理解**

    1. 作用对象

       - **方法**:可以将一个方法声明为 synchronized，此时整个方法体都是同步的，只有持有该对象锁的线程可以执行该方法，其他线程会被阻塞，直到锁被释放.

       - **代码块**：也可以将代码块声明为 synchronized，只同步代码块内部的部分逻辑，而不是整个方法。这样可以提高程序的效率，避免不必要的阻塞。

    2. **对象锁**：

       - 当使用 synchronized 修饰实例方法或同步代码块时，线程需要获取当前对象实例的锁。

       - 对于静态方法，线程需要获取类对象的锁，因为静态方法属于类，而非类的实例。

    3. **重入锁**：synchronized 是一种重入锁，意味着如果一个线程已经获取了对象的锁，它可以继续执行该对象的其他 synchronized 方法或代码块，而不会被阻塞.（important）

    4. 性能问题：由于 synchronized 会阻塞其他线程的访问，使用过多的同步可能会降低系统性能，**导致线程竞争和上下文切换频繁**。因此，在使用 synchronized 时，**应尽量减少锁的持有时间，避免不必要的同步**。(Important)

       与其他锁的比较：在高并发环境下，synchronized 的性能较为有限。为此，Java 提供了其他更灵活的锁机制，如 ReentrantLock，它支持更加细粒度的锁控制和中断响应机制。

    5. 总结来说，synchronized 是 Java 提供的基础线程同步工具，用于确保共享资源在并发情况下的安全访问，但也要注意合理使用，避免影响程序的性能。

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

    <img src="https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250910-160917-image-20250910160914273.png" alt="image-20250910160914273" style="zoom:50%;float:left" />

    execute()：线程池的核心方法。任务提交后，如果线程数少于核心线程数，直接创建新线程；否则，将任务加入阻塞队列。如果队列满了并且线程数量小于最大线程数，则继续创建新线程处理任务。

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

