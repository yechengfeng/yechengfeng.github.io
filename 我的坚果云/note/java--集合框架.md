- # Java 集合框架总结

  ## List

  > - #### **ArrayList**
  >   - 基于数组实现，无容量的限制。
  >   - 在执行插入元素时可能要扩容，在删除元素时并不会减小数组的容量，在查找元素时要遍历数组，对于非null的元素采取equals的方式寻找。
  >   - 是非线程安全的。
  >   - **注意点**
  >     - 容器底层采用数组存储，每次扩容为1.5倍
  >     - ArrayList随机元素访问时间复杂度O(1)，插入删除操作需大量移动元素，效率较低
  >     - DEFAULT_CAPACITY =10,默认大小十个，即使指定的小于10个，也是10个
  >
  > - #### **LinkedList**
  >   - 基于双向链表机制
  >   - 在插入元素时，须创建一个新的Entry对象，并切换相应元素的前后元素的引用；在查找元素时，须遍历链表；在删除元素时，须遍历链表，找到要删除的元素，然后从链表上将此元素删除即可。
  >   - 是非线程安全的
  >   - **注意点**
  >     1. LinkedList有两个构造参数，一个为无参构造，只是新建一个空对象，第二个为有参构造，新建一个空对象，然后把所有元素添加进去。
  >     2. LinkedList的存储单元为一个名为Node的内部类，包含pre指针，next指针，和item元素，实现为双向链表
  >     3. LinkedList的删除、添加操作时间复杂度为O(1)，查找时间复杂度为O(n)，查找函数有一定优化，容器会先判断查找的元素是离头部较近，还是尾部较近，来决定从头部开始遍历还是尾部开始遍历
  >     4. LinkedList实现了Deque接口，因此也可以作为栈、队列和双端队列来使用。
  >     5. LinkedList可以存储null值
  >
  > - #### **Vector**
  >   - 基于synchronized实现的线程安全的ArrayList，但在插入元素时容量扩充的机制和ArrayList稍有不同，并可通过传入capacityIncrement来控制容量的扩充。
  >
  > - #### **CopyOnWriteArrayList**
  >   - 是一个线程安全、并且在读操作时无锁的ArrayList。
  >   - 很多时候，我们的系统应对的都是**读多写少的并发场景**。CopyOnWriteArrayList容器允许并发读，读操作是无锁的，性能较高。至于写操作，比如向容器中添加一个元素，则首先将当前容器复制一份，然后在新副本上执行写操作，结束之后再将原容器的引用指向新容器。
  >   - **优点**
  >     1. 采用读写分离方式，读的效率非常高
  >     2. CopyOnWriteArrayList的迭代器是基于创建时的数据快照的，故数组的增删改不会影响到迭代器
  >   - **缺点**
  >     1. 内存占用高，每次执行写操作都要将原容器拷贝一份，数据量大时，对内存压力较大，可能会引起频繁GC
  >     2. 只能保证数据的最终一致性，不能保证数据的实时一致性。写和读分别作用在新老不同容器上，在写操作执行过程中，读不会阻塞但读取到的却是老容器的数据。
  >

  ## Set

  - **HashSet**（底层是HashMap)
  - **TreeSet**（底层是TreeMap)
  - **LinkedHashSet**（继承自HashSet，底层是LinkedHashMap)
    - LinkedHashMap。这样做的意义或者好处就是LinkedHashSet中的元素顺序是可以保证的，也就是说遍历序和插入序是一致的。
  - **BitSet**（位集，底层是long数组，用于替代List<Boolean>)
  - **CopyOnWriteArraySet**（底层是CopyOnWriteArrayList)

  ## Queue

  > 先进先出（FIFO—first in first out）的线性表

  ### 1. ArrayDeque

  - 底层是循环数组，有界队列

  ### 2.ConcurrentLinkedQueue

  - 底层是链表，基于CAS的非阻塞队列，无界队列

  ### 3. ConcurrentLinkedDeque
  - 底层是双向链表，基于CAS的非阻塞队列，无界队列

  ### 4.PriorityQueue
  - 底层是数组，逻辑上是小顶堆，无界队列

  ### 5.ArrayBlockingQueue
  - 底层是数组，阻塞队列，一把锁两个Condition，有界同步队列

  ### 6.LinkedBlockingQueue
  - 底层是链表，阻塞队列，两把锁，各自对应一个Condition，无界同步队列
    - 另一种BlockingQueue的实现，基于链表，没有容量限制。
    - 由于出队只操作队头，入队只操作队尾，这里巧妙地使用了两把锁，对于put和offer入队操作使用一把锁，对于take和poll出队操作使用一把锁，避免了出队、入队时互相竞争锁的现象，因此LinkedBlockingQueue在高并发读写都多的情况下，性能会较ArrayBlockingQueue好很多，在遍历以及删除的情况下则要两把锁都要锁住。
    - 多CPU情况下可以在同一时刻既消费又生产。

  ### 7.PriorityBlockingQueue
  - 底层是数组，出队时队空则阻塞；无界队列，不存在队满情况，一把锁一个Condition

  ### 8.DelayQueue
  - 底层是PriorityQueue，无界阻塞队列，过期元素方可移除，基于Lock

  ### 9.SynchronousQueue
  - 只存储一个元素，阻塞队列，基于CAS

  ### 10.TransferQueue
  - LinkedTransferQueue（底层是链表，阻塞队列，无界同步队列)

  ### 11.Queue实现类区别
  - 非线程安全：ArrayDeque、LinkedList、PriorityQueue
  - 线程安全：ConcurrentLinkedQueue、ConcurrentLinkedDeque、ArrayBlockingQueue、LinkedBlockingQueue、PriorityBlockingQueue
  - 线程安全又分为阻塞队列和非阻塞队列：
    - 阻塞队列提供 put、take 等会阻塞当前线程的方法
    - 非阻塞队列使用 CAS 保证线程安全，无需加锁，不阻塞当前线程

  ### 12.ArrayBlockingQueue 和 LinkedBlockingQueue 区别
  - 队列中锁的实现不同：
    - ArrayBlockingQueue：生产和消费用的是同一个锁
    - LinkedBlockingQueue：生产用 putLock，消费用 takeLock（锁分离）
  - 底层实现不同：
    - ArrayBlockingQueue：数组
    - LinkedBlockingQueue：链表
  - 队列边界不同：
    - ArrayBlockingQueue：有界
    - LinkedBlockingQueue：可不指定大小，默认 Integer.MAX_VALUE，无界队列

  ## Map

  - **HashMap**（底层是数组+链表/红黑树，无序键值对集合，非线程安全)
    - [https://blog.csdn.net/jiary5201314/article/details/51439982](https://blog.csdn.net/jiary5201314/article/details/51439982)
    - HashCode计算：
      - [https://www.zhihu.com/question/20733617/answer/111577937](https://www.zhihu.com/question/20733617/answer/111577937)
      - [https://blog.csdn.net/justloveyou_/article/details/62893086](https://blog.csdn.net/justloveyou_/article/details/62893086)

  - **Hashtable**
    - [https://blog.csdn.net/ns_code/article/details/36191279](https://blog.csdn.net/ns_code/article/details/36191279)
    - 基于哈希表实现，每个元素是 key-value，容量不足时自动增长
    - 线程安全，实现 Serializable 接口和 Cloneable 接口

  - **LinkedHashMap**
    - 底层是(数组+链表/红黑树)+环形双向链表，继承自 HashMap

  - **TreeMap**
    - 底层红黑树

  - **ConcurrentHashMap**
    - 底层数组+链表/红黑树，基于 CAS+synchronized
    - 参考：
      - [https://www.cnblogs.com/chengxiao/p/6842045.html](https://www.cnblogs.com/chengxiao/p/6842045.html)
      - [http://ifeve.com/hashmap-concurrenthashmap-%E7%9B%B8%E4%BF%A1%E7%9C%8B%E5%AE%8C%E8%BF%99%E7%AF%87%E6%B2%A1%E4%BA%BA%E8%83%BD%E9%9A%BE%E4%BD%8F%E4%BD%A0%EF%BC%81/](http://ifeve.com/hashmap-concurrenthashmap-%E7%9B%B8%E4%BF%A1%E7%9C%8B%E5%AE%8C%E8%BF%99%E7%AF%87%E6%B2%A1%E4%BA%BA%E8%83%BD%E9%9A%BE%E4%BD%8F%E4%BD%A0%EF%BC%81/)
    
  - **ConcurrentSkipListMap**
    - key 有序，支持高并发，存取时间 log(N)
    - 底层跳表结构
  
  ### Map实现类之间的区别
  - HashMap 与 ConcurrentHashMap
    - 前者允许 key 或 value 为 null，后者不允许
    - 前者非线程安全，后者线程安全
  - HashMap、TreeMap 与 LinkedHashMap
    - HashMap 遍历顺序随机
    - TreeMap 可排序
    - LinkedHashMap 按插入/访问顺序排序，get 效率 O(1) > TreeMap O(log n)
  - ConcurrentHashMap、Collections.synchronizedMap 与 Hashtable
    - 锁粒度不同
    - ConcurrentHashMap 分段 + CAS，无锁优化
    - Hashtable & synchronizedMap 方法加锁实现，锁粒度大
    - ConcurrentHashMap 提供 putIfAbsent 等方法
  
  ---
  
  ## Fail-Fast
  
  - 集合中 modCount 记录修改次数
  - 迭代器遍历时赋予 expectedModCount，如果遍历过程中 modCount != expectedModCount，抛出 ConcurrentModificationException
  - 原因：迭代器在遍历时直接访问集合内容，集合在遍历期间结构变化会改变 modCount
  - 解决办法：使用线程安全集合（CopyOnWriteArrayList、ConcurrentHashMap 等）
