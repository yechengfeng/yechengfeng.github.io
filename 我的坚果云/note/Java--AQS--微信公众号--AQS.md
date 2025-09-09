- https://javadoop.com/post/AbstractQueuedSynchronizer

- https://www.cnblogs.com/star95/p/17656890.html

- 类变量

  - private transient volatile Node head; --头结点

  - private transient volatile Node tail; --尾结点

  - private volatile int state; 代表锁得状态

  - private transient Thread exclusiveOwnerThread; //继承自AbstractOwnableSynchronizer reentrantLock.lock() 可以嵌套调用多次，所以每次用这个来判断当前线程是否已经拥有了锁
    - ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/note20250909-123416-8209378_e6c63544-6fc5-4f47-97c0-4abffa5ed8f0-20250909111435944.png)

等待队列中每个线程被包装成一个 Node 实例，数据结构是链表.

