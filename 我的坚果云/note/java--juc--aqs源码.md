1. AQS核心

   - int state 
   - Node对象组成的双向链表
     - 比如ReentrantLock,有一个线程没有拿到锁资源，当前线程需要等一会，需要将封装为Node对象，将Node添加到双向链表，讲线程挂起，等待即可
     - 为啥等待用双向链表，因为可以取消，用双向链表方便
   - Node对象组成的单向链表
     - 比如ReentrantLock,一个线程持有锁的时候，执行了wait方法，此事这个线程就需要封装成Node对象，添加到单向链表

2. AQS父类

   - ```java
     AbstractOwnableSynchronizer
         private transient Thread exclusiveOwnerThread; //记录独占模式下当前同步器的持有线程;
     
     
     
     ```

3. AQS在ReentrantLock中流程

4. > 当new了一个ReentrantLock,AQS 默认  state =0 ; head =null; tail =null; 
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

5. AQS&Lock锁的tryAcquire

   - ReentrantLock的lock是 SYNC实现的
   - Sync是一个抽象类，集成了AQS ,实现类有俩个
     - FailSync
     - NonFairSync

   ```java
   
   //非公平锁的实现
   public void lock() {
       sync.lock(); // sync是AbstractQueuedSynchronizer的子类
   }
   
   final void lock() {
       if (compareAndSetState(0, 1)) { // 尝试直接获取锁
           setExclusiveOwnerThread(Thread.currentThread());
       } else {
           acquire(1); // 获取失败就排队
       }
   }
   
   ```

   ```java
   public void lock() {
       sync.lock();
   }
   
   final void lock() {
       acquire(1);
   }
   public final void acquire(int arg) {
     // 1. 查看tryAcquire
     // 2. 查看addWaiter 没拿到锁 去排队
     // 3. 查看acquireQueued,挂起线程和后续被唤醒继续锁资源的逻辑
       if (!tryAcquire(arg) && 
           acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) {
           selfInterrupt();
       }
   }
   
   //这个方法是AQS实现
   protected final boolean tryAcquire(int acquires) {
       final Thread current = Thread.currentThread();
       int c = getState();
       if (c == 0) {
           // 公平性判断  //hasQueuedPredecessors() 返回 true 表示队列中已有比当前线程先来的线程。
           if (!hasQueuedPredecessors() && compareAndSetState(0, 1)) {
               setExclusiveOwnerThread(current);
               return true;
           }
       } else if (current == getExclusiveOwnerThread()) {
           setState(c + acquires);
           return true;
       }
       return false;
   }
   
   ```

   