**JUC包提供了一些锁、同步工具、线程池、并发集合**

##### 1. CountDownLatch

> ##### 构造方法只有一个
>
> ```java
> public CountDownLatch(int count)	;//count位初始计数值
> ```
>
> ##### 常用的方法
>
> ```java
> public void await() throws InterruptedException{}//常用方法;调用此方法线程会被挂起，它会等待直到count的值为0才继续执行
> 
> ```
>
> ```java
> public void await(long timeout ,TimeUnit unit) throws InterruptedException{} //和await类似，但是会等待一个固定的时间.	
> ```
>
> ```java
> public void countDown();//将count的值减1
> ```
>
> ##### 使用场景
>
> 用户购买一个商品下单成功后，我们会给用户发送各种消息提示用户『购买成功』，比如发送邮件、微信消息、短信等。所有的消息都发送成功后，我们在后台记录一条消息表示成功。
>
> 实现原理
>
> <img src="https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250910-153311-image-20250910153308970.png" alt="image-20250910153308970" style="zoom:25%; float :left" />
>
> - `Sync`是`CountDownLatch`的内部类，被成员变量`sync`持有。`Sync`继承了`AbstractQueuedSynchronizer`，
>
>   ```java
>   // CountDownLatch#await ;此方法会阻塞等待其他线程完成任务
>   public void await() throws InterruptedException {
>       // 调用AbstractQueuedSynchronizer的acquireSharedInterruptibly方法
>       sync.acquireSharedInterruptibly(1);
>   }
>   // AbstractQueuedSynchronizer#acquireSharedInterruptibly
>   public final void acquireSharedInterruptibly(int arg) throws InterruptedException {
>       // 判断线程是否被打断，抛出打断异常
>       if (Thread.interrupted())
>           throw new InterruptedException();
>       // 尝试获取共享锁
>       // 条件成立说明 state > 0，此时线程入队阻塞等待，等待其他线程获取共享资源
>       // 条件不成立说明 state = 0，此时不需要阻塞线程，直接结束函数调用
>       if (tryAcquireShared(arg) < 0)
>           // 阻塞当前线程的逻辑
>           doAcquireSharedInterruptibly(arg);
>   }
>   // CountDownLatch.Sync#tryAcquireShared
>   protected int tryAcquireShared(int acquires) {
>       return (getState() == 0) ? 1 : -1;
>   }
>   ```
>
>   ```java
>   // AbstractQueuedSynchronizer#doAcquireSharedInterruptibly
>   private void doAcquireSharedInterruptibly(int arg) throws InterruptedException {
>       // 将调用latch.await()方法的线程 包装成 SHARED 类型的 node 加入到 AQS 的阻塞队列中
>       final Node node = addWaiter(Node.SHARED);
>       boolean failed = true;
>       try {
>           for (;;) {
>               // 获取当前节点的前驱节点
>               final Node p = node.predecessor();
>               // 前驱节点是头节点就可以尝试获取锁
>               if (p == head) {
>                   // 再次尝试获取锁，获取成功返回 1
>                   int r = tryAcquireShared(arg);
>                   if (r >= 0) {
>                       // 获取锁成功，设置当前节点为 head 节点，并且向后传播
>                       setHeadAndPropagate(node, r);
>                       p.next = null; // help GC
>                       failed = false;
>                       return;
>                   }
>               }
>               // 阻塞在这里
>               if (shouldParkAfterFailedAcquire(p, node) && parkAndCheckInterrupt())
>                   throw new InterruptedException();
>           }
>       } finally {
>           // 阻塞线程被中断后抛出异常，进入取消节点的逻辑
>           if (failed)
>               cancelAcquire(node);
>       }
>   }
>   ```
>
>   ```java
>   private final boolean parkAndCheckInterrupt() {
>       	// 阻塞线程
>           LockSupport.park(this);
>           return Thread.interrupted();
>       }
>   ```
>
>   ```java
>   //任务结束调用 countDown() 完成计数器减一（释放锁）的操作
>   public void countDown() {
>       sync.releaseShared(1);
>   }
>   
>   public final boolean releaseShared(int arg) {
>       // 尝试释放共享锁
>       if (tryReleaseShared(arg)) {
>           // 释放锁成功开始唤醒阻塞节点
>           doReleaseShared();
>           return true;
>       }
>       return false;
>   }
>   ```
>
>   ```java
>   protected boolean tryReleaseShared(int releases) {
>       for (;;) {
>           int c = getState();
>           // 条件成立说明前面【已经有线程触发唤醒操作】了，这里返回 false
>           if (c == 0)
>               return false;
>           // 计数器减一
>           int nextc = c-1;
>           if (compareAndSetState(c, nextc))
>               // 计数器为 0 时返回 true
>               return nextc == 0;
>       }
>   }
>   ```
>
>   ```java
>   private void doReleaseShared() {
>       for (;;) {
>           Node h = head;
>           // 判断队列是否是空队列
>           if (h != null && h != tail) {
>               int ws = h.waitStatus;
>               // 头节点的状态为 signal，说明后继节点没有被唤醒过
>               if (ws == Node.SIGNAL) {
>                   // cas 设置头节点的状态为 0，设置失败继续自旋
>                   if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
>                       continue;
>                   // 唤醒后继节点
>                   unparkSuccessor(h);
>               }
>               // 如果有其他线程已经设置了头节点的状态，重新设置为 PROPAGATE 传播属性
>               else if (ws == 0 && !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
>                   continue;
>           }
>           // 条件不成立说明被唤醒的节点非常积极，直接将自己设置为了新的head，
>           // 此时唤醒它的节点（前驱）执行 h == head 不成立，所以不会跳出循环，会继续唤醒新的 head 节点的后继节点
>           if (h == head)
>               break;
>       }
>   }
>   ```
>
>   



##### 2. ThreadLocal

> - #### 原理
>
>   - 每个线程是一个Thread实例，其内部维护一个threadLocals的实例成员，其类型是ThreadLocal.ThreadLocalMap。
>   - 通过实例化ThreadLocal实例，我们可以对当前运行的线程设置一些线程私有的变量，通过调用ThreadLocal的set和get方法存取。
>   - ThreadLocal本身并不是一个容器，我们存取的value实际上存储在ThreadLocalMap中，ThreadLocal只是作为TheadLocalMap的key。
>   - 每个线程实例都对应一个TheadLocalMap实例，我们可以在同一个线程里实例化很多个ThreadLocal来存储很多种类型的值，这些ThreadLocal实例分别作为key，对应各自的value，最终存储在Entry table数组中。
>   - 当调用ThreadLocal的set/get进行赋值/取值操作时，首先获取当前线程的ThreadLocalMap实例，然后就像操作一个普通的map一样，进行put和get。
>
> - ##### 内存模型
>
>   ![image-20250910132543155](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250910-132545-image-20250910132543155.png)
>
>   - 图中左边是栈，右边是堆。线程的一些局部变量和引用使用的内存属于Stack（栈）区，而普通的对象是存储在Heap（堆）区。
>
>     - 线程运行时，我们定义的TheadLocal对象被初始化，存储在Heap，同时线程运行的栈区保存了指向该实例的引用，也就是图中的ThreadLocalRef。
>
>     - 当ThreadLocal的set/get被调用时，虚拟机会根据当前线程的引用也就是CurrentThreadRef找到其对应在堆区的实例，然后查看其对用的TheadLocalMap实例是否被创建，如果没有，则创建并初始化。
>
>     - Map实例化之后，也就拿到了该ThreadLocalMap的句柄，那么就可以将当前ThreadLocal对象作为key，进行存取操作。
>
>     - 图中的虚线，表示key对应ThreadLocal实例的引用是个弱引用。
>
> - ##### 关于内存泄漏的问题
>
>   - 如果key 使用使用强引用
>
>     - 引用ThreadLocal的对象被回收了，但是ThreadLocalMap还持有ThreadLocal的强引用，如果没有手动删除，ThreadLocal不会被回收，导致Entry内存泄漏。
>
>   - key 使用弱引用
>
>     - 引用ThreadLocal的对象被回收了，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。value在下一次ThreadLocalMap调用set、get、remove的时候会被清除。
>
>     - 比较两种情况，我们可以发现：由于ThreadLocalMap的生命周期跟Thread一样长，如果都没有手动删除对应key，都会导致内存泄漏，但是使用弱引用可以多一层保障：弱引用ThreadLocal被清理后key为null，对应的value在下一次ThreadLocalMap调用set、get、remove的时候可能会被清除。
>
>     - 因此，ThreadLocal内存泄漏的根源是：由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。
>
> - #### ThreadLocal最佳实践
>
>   - 使用场景
>
>     - 当需要存储线程私有变量的时候。
>
>     - 当需要实现线程安全的变量时。
>
>     - 当需要减少线程资源竞争的时候。
>
> - #### 注意
>
>   - **每次使用完ThreadLocal，建议调用它的remove()方法，清除数据。**
>
>   - **尽量用全局变量 ，用static修饰**

#### 3.CyclicBarrier

> - CyclicBarrier 允许一组线程彼此等待，直到所有线程都达到某个屏障点，屏障可以重复使用。
> - await()：线程调用 await() 方法会在屏障处等待，直到所有线程都调用了该方法。当所有线程到达屏障时，屏障会打开，并允许所有线程继续执行。
> - 工作原理:
>   - 通过 ReentrantLock 和 Condition 来实现线程的等待与唤醒。当所有线程都到达屏障点时，signalAll() 唤醒所有等待的线程。

#### 4.Semaphore

> 1. <img src="https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250910-161123-8209378_33486757-89d7-4d6e-be8c-ad4ea722e594.png" alt="img" style="zoom:40%; float:left" />
>
> 
>
> Semaphore 是一种基于计数的信号量，用于控制同时访问某一资源的线程数量。
>
> acquire()：线程会尝试获取信号量，如果可用的许可（permits）为 0，则线程会阻塞直到有可用的许可。
>
> release()：释放许可，唤醒等待中的线程。
>
> 工作原理：
>
> ​	Semaphore 内部通过 AbstractQueuedSynchronizer 来管理信号量。
>
> ​	信号量通过一个计数器（permits）来限制同时进入临界区的线程数，每当线程进入临界区时，计数器减少，线程离开时，计数器增加。
