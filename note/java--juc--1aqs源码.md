

QAS参考

[https://javadoop.com/post/AbstractQueuedSynchronizer](https://javadoop.com/post/AbstractQueuedSynchronizer)

[https://javadoop.com/post/AbstractQueuedSynchronizer-2](https://javadoop.com/post/AbstractQueuedSynchronizer-2)

[https://javadoop.com/post/AbstractQueuedSynchronizer-3](https://javadoop.com/post/AbstractQueuedSynchronizer-3)



1. AQS的核心字段

   > ```java
   > public abstract class AbstractQueuedSynchronizer
   >     extends AbstractOwnableSynchronizer
   >     implements java.io.Serializable {
   > 
   >     private volatile transient Node head; // 队列头 head：队列的头节点，通常是正在持有锁的线程（或虚拟节点）。比如ReentrantLock,有一个线程没有拿到锁资源，当前线程需要等一会，需要将其封装为Node对象，将Node添加到双向链表，将当前线程挂起，等待即可 ; 为啥等待用双向链表，因为可以取消，用双向链表方便 ,只要把当前节点的前驱节点当前节点的后驱节点，后驱节点的前驱变成当前前驱的后驱。
   >     private volatile transient Node tail; // 同步队列尾节点
   >     private volatile int state;           // 同步状态
   > }
   > 父类
   > AbstractOwnableSynchronizer
   >     private transient Thread exclusiveOwnerThread; //记录独占模式下当前同步器的持有线程;
   > ```

2. AQS核心变量

   - **int state** 
   - **Node对象组成的双向链表**
     - 比如ReentrantLock,有一个线程没有拿到锁资源，当前线程需要等一会，需要将其封装为Node对象，将Node添加到双向链表，将当前线程挂起，等待即可
     - 
   - **Node对象组成的单向链表**
     - 比如ReentrantLock,一个线程持有锁的时候，执行了wait方法，此事这个线程就需要封装成Node对象，添加到单向链表

3. AQS 使用 **双向链表** 来管理等待线程队列，每个节点对应一个线程和等待状态：

   ```java
   //node 节点源码
   static final class Node {
       /** 等待线程状态常量 */
       static final int CANCELLED =  1; // 取消
       static final int SIGNAL    = -1; // 后继线程需要唤醒
       static final int CONDITION = -2; // 条件队列中
       static final int PROPAGATE = -3; // 用于共享模式
   
       volatile int waitStatus;        // 节点状态
       volatile Node prev;             // 前驱节点
       volatile Node next;             // 后继节点
       volatile Thread thread;         // 当前节点对应的线程
       Node nextWaiter;                // 条件队列的 next 节点
   
       final boolean isShared() {
           return nextWaiter == SHARED;
       }
   
       Node() {} // 空构造函数
   
       Node(Thread thread, Node mode) { // 用于加入队列
           this.thread = thread;
           this.nextWaiter = mode;
       }
   
       Node(Thread thread, int waitStatus) { // 用于条件队列
           this.thread = thread;
           this.waitStatus = waitStatus;
       }
   
       static final Node SHARED = new Node();
       static final Node EXCLUSIVE = null;
   }
   
   ```

4. AQS在ReentrantLock中流程

   > 当new了一个ReentrantLock,AQS 默认  state =0 ; head =null; tail =null; 
   >
   > A线程执行了lock方法，获取锁资源
   >
   > - A线程将state从0改成1，代表获取锁资源成功
   >
   > B线程要获取锁资源，但是锁资源被A线程持有
   >
   > - B线程获取锁资源失败，需要去排队，添加到双向链表中（顺序不能错误）
   >   - 1.先把自己的前驱节点指向head
   >   - 2.再把CAS把tail设置成自己
   >   - 3.再把head的后驱节点指向自己。
   >
   > 挂起B线程，等待A线程释放锁资源，然后唤起B线程。
   >
   > A线程释放锁资源，将state从1改成0，唤醒head.next节点，
   >
   > B线程就可以重新尝试获取锁资源

- 先从 最基本的代码分析

  ```java
  //非公平锁的实现 
   ReentrantLock reentrantLock =new ReentrantLock();
    reentrantLock.lock(); //看一下Lock干了啥事 ;lock使用SYNC实现的，SYNC 集成了AQS实现了非公平和公平俩个版本
  //AQS中提供的tryAcquire直接抛出了异常,逻辑在子类实现 
  ```

```java
 //这段代码是在AQS实现的。放在这里便于查看
  public final void acquire(int arg) {
  // 1. 查看tryAcquire
  // 2. 查看addWaiter 没拿到锁去排队
  // 3. 查看acquireQueued,挂起线程和后续被唤醒继续锁资源的逻辑
    if (!tryAcquire(arg) && 
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) {
        selfInterrupt();
    }
static final class FairSync extends Sync {
  //公平锁的实现
public void lock() {
    sync.lock();
}
final void lock() {
  //acquire方法是AQS实现的
    acquire(1);
}
  //这个是公平锁的实现，和非公平唯一区别是要判断自己是不是第一个 hasQueuedPredecessors
 protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            // 公平锁：检查是否有前面等待的线程,没有线程排队就抢锁
          	//如果有线程排队，查看自己是不是排在第一位，如果是，就开始抢锁
            if (!hasQueuedPredecessors() && compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
          //查看当前持有锁的线程是不是自己
        } else if (current == getExclusiveOwnerThread()) {
            // 重入处理
            int nextc = c + acquires;
            if (nextc < 0) // overflow 
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
   //如果执行失败了，那么返回到AQS代码继续执行
        return false;
    }
}
}

```

```java
//非公平锁的实现 
 ReentrantLock reentrantLock =new ReentrantLock();
        reentrantLock.lock();

public void lock() {
    sync.lock(); // sync是AbstractQueuedSynchronizer的子类
}
 static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;
   //	走来先CAS抢占锁
        final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }
        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
    }
//非公平锁的tryAcquire，可以看到和上面的就差了一个 hasQueuedPredecessors
   final boolean nonfairTryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
}

```

如果没有当前线程没有抢占锁成功，**接下来会执行 AQS的addWaiter方法**

```java
//1.tail != null 表示队列已经初始化过。
//2.先设置 node.prev = tail，再 CAS 更新 tail 指向新节点。
//3.如果 CAS 成功：
//4.将前驱节点的 next 指向新节点。
//5.返回新节点。
//6.这是 快速路径，多线程高并发情况下会减少锁操作。
private Node addWaiter(Node mode) {
    // 1. 创建新节点
    Node node = new Node(Thread.currentThread(), mode);
    // 2. 快速尝试直接加入队尾
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    // 3. 队列未初始化或 CAS 失败，调用 enq 方法入队
    enq(node);
    return node;
}
队列的入队方法
//如果队列为空，先初始化一个虚拟头节点。
//否则，将新节点加入尾部，更新 prev 和 next 指针。
//使用 CAS 保证多线程下安全。
private Node enq(final Node node) {
  //自旋入队
    for (;;) {
        Node t = tail;
        if (t == null) { // 队列为空，初始化虚拟节点
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

这个是核心方法，线程挂起和唤醒的逻辑

```java
//线程Node添加到AQS队列之后的操作
final boolean acquireQueued(final Node node, int arg) {
  //标记 代表拿锁失败
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
          //获取 前一个节点
            final Node p = node.predecessor();
         	// 条件：前驱节点是头节点 并且 tryAcquire(arg) 成功 
            if (p == head && tryAcquire(arg)) {
              // //将当前节点设置为新的 head
                setHead(node);
               //断开前驱的 next，帮助 GC
                p.next = null; // help GC
                failed = false;
               //返回是否被中断（interrupted）
                return interrupted;
            }
          // ws =1 代表当前节点取消了
          //ws =-1 代表当前节点的next节点挂起了
          //没有拿到锁，可以不可以挂起  1. 检查前驱节点状态 waitStatus (默认都是0,) 2.确保只有前驱节点已经设置 SIGNAL，才允许自己阻塞    然后parkAndCheckInterrupt -> 调用 LockSupport.park(this) 阻塞当前线程
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}

private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)  // 前驱节点已经设置 SIGNAL，说明自己可以阻塞
        return true;
    if (ws > 0) { // 前驱节点被取消
        // 跳过被取消的节点
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        // 前驱节点状态不是 SIGNAL，也不是取消，设置 SIGNAL
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}

```

**释放锁的核心逻辑**

```java
public final boolean release(int arg) {
    if (tryRelease(arg)) {
        Node h = head;
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h); // 唤醒下一个线程
        return true;
    }
    return false;
}

//唤醒下一个等待线程（出队）。
//如果下一个节点被取消（waitStatus > 0），则向前寻找可唤醒线程。
//使用 LockSupport.unpark 唤醒线程。
private void unparkSuccessor(Node node) {
    int ws = node.waitStatus;
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);

    Node s = node.next;
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    if (s != null)
        LockSupport.unpark(s.thread);
}


```

