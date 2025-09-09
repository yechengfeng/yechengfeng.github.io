- **List**

  - ArrayList

    - 基于数组实现，无容量的限制。

    - 在执行插入元素时可能要扩容，在删除元素时并不会减小数组的容量，在查找元素时要遍历数组，对于非null的元素采取equals的方式寻找。

    - 是非线程安全的。

    - 注意点

      - 容器底层采用数组存储，每次扩容为1.5倍

      - ArrayList随机元素时间复杂度O(1)，插入删除操作需大量移动元素，效率较低

      - DEFAULT_CAPACITY =10,默认大小十个，即使指定的小于10个，也是10个

  - LinkedList

    - 基于双向链表机制

    - 在插入元素时，须创建一个新的Entry对象，并切换相应元素的前后元素的引用；在查找元素时，须遍历链表；在删除元素时，须遍历链表，找到要删除的元素，然后从链表上将此元素删除即可。

    - 是非线程安全的

    - 注意点

      - （1)LinkedList有两个构造参数，一个为无參构造，只是新建一个空对象，第二个为有参构造，新建一个空对象，然后把所有元素添加进去。

      - （2)LinkedList的存储单元为一个名为Node的内部类，包含pre指针，next指针，和item元素，实现为双向链表

      - （3)LinkedList的删除、添加操作时间复杂度为O(1)，查找时间复杂度为O(n)，查找函数有一定优化，容器会先判断查找的元素是离头部较近，还是尾部较近，来决定从头部开始遍历还是尾部开始遍历

      - （4)LinkedList实现了Deque接口，因此也可以作为栈、队列和双端队列来使用。

      - （5)LinkedList可以存储null值

  - Vector
    - 基于synchronized实现的线程安全的ArrayList，但在插入元素时容量扩充的机制和ArrayList稍有不同，并可通过传入capacityIncrement来控制容量的扩充。

  - CopyOnWriteArrayList

    - 是一个线程安全、并且在读操作时无锁的ArrayList。

    - 很多时候，我们的系统应对的都是读多写少的并发场景。CopyOnWriteArrayList容器允许并发读，读操作是无锁的，性能较高。至于写操作，比如向容器中添加一个元素，则首先将当前容器复制一份，然后在新副本上执行写操作，结束之后再将原容器的引用指向新容器。

    - 优点

      - 1)采用读写分离方式，读的效率非常高

      - 2)CopyOnWriteArrayList的迭代器是基于创建时的数据快照的，故数组的增删改不会影响到迭代器

    - 缺点

      - 1)内存占用高，每次执行写操作都要将原容器拷贝一份，数据量大时，对内存压力较大，可能会引起频繁GC

      - 2)只能保证数据的最终一致性，不能保证数据的实时一致性。写和读分别作用在新老不同容器上，在写操作执行过程中，读不会阻塞但读取到的却是老容器的数据。

- **Set**

  - HashSet（底层是HashMap)

  - TreeSet（底层是TreeMap)

  - LinkedHashSet（继承自HashSet，底层是LinkedHashMap)
    - LinkHashMap。这样做的意义或者好处就是LinkedHashSet中的元素顺序是可以保证的，也就是说遍历序和插入序是一致的。

  - BitSet（位集，底层是long数组，用于替代List<Boolean>

  - CopyOnWriteArraySet（底层是CopyOnWriteArrayList)

- **Queue**

  - ​	先进先出”（FIFO—first in first out)的线性表

  - 1)ArrayDeque（底层是循环数组，有界队列)

    - ConcurrentLinkedQueue（底层是链表，基于CAS的非阻塞队列，无界队列)

    - ConcurrentLinkedDeque（底层是双向链表，基于CAS的非阻塞队列，无界队列)

  - 2)PriorityQueue（底层是数组，逻辑上是小顶堆，无界队列)

    - ArrayBlockingQueue（底层是数组，阻塞队列，一把锁两个Condition，有界同步队列)

    - LinkedBlockingQueue（底层是链表，阻塞队列，两把锁，各自对应一个Condition，无界同步队列)

      - 另一种BlockingQueue的实现，基于链表，没有容量限制。

      - 由于出队只操作队头，入队只操作队尾，这里巧妙地使用了两把锁，对于put和offer入队操作使用一把锁，对于take和poll出队操作使用一把锁，避免了出队、入队时互相竞争锁的现象，因此LinkedBlockingQueue在高并发读写都多的情况下，性能会较ArrayBlockingQueue好很多，在遍历以及删除的情况下则要两把锁都要锁住。

      - 多CPU情况下可以在同一时刻既消费又生产。

    - PriorityBlockingQueue（底层是数组，出队时队空则阻塞；无界队列，不存在队满情况，一把锁一个Condition)
    - DelayQueue（底层是PriorityQueue，无界阻塞队列，过期元素方可移除，基于Lock)
    - SynchronousQueue（只存储一个元素，阻塞队列，基于CAS)

  - 4)TransferQueue（特殊的BlockingQueue)
    - LinkedTransferQueue（底层是链表，阻塞队列，无界同步队列)

  - 5)Queue实现类之间的区别

    - 非线程安全的：ArrayDeque、LinkedList、PriorityQueue

    - 线程安全的：ConcurrentLinkedQueue、ConcurrentLinkedDeque、ArrayBlockingQueue、LinkedBlockingQueue、PriorityBlockingQueue

    - 线程安全的又分为阻塞队列和非阻塞队列，阻塞队列提供了put、take等会阻塞当前线程的方法，比如ArrayBlockingQueue、LinkedBlockingQueue、PriorityBlockingQueue，也有offer、poll等阻塞一段时间候返回的方法；

    - 非阻塞队列是使用CAS机制保证offer、poll等可以线程安全地入队出队，并且不需要加锁，不会阻塞当前线程，比如ConcurrentLinkedQueue、ConcurrentLinkedDeque。

- ArrayBlockingQueue和LinkedBlockingQueue 区别

  - 队列中锁的实现不同

    - ArrayBlockingQueue实现的队列中的锁是没有分离的，即生产和消费用的是同一个锁；

    - LinkedBlockingQueue实现的队列中的锁是分离的，即生产用的是putLock，消费是takeLock

  - 底层实现不同
    - 前者基于数组，后者基于链表

  - 队列边界不同

    - ArrayBlockingQueue实现的队列中必须指定队列的大小，是有界队列

    - LinkedBlockingQueue实现的队列中可以不指定队列的大小，但是默认是Integer.MAX_VALUE，是无界队列

- Map

  - HashMap（底层是数组+链表/红黑树，无序键值对集合，非线程安全)

    - https://blog.csdn.net/jiary5201314/article/details/51439982

    - HashCode 计算

      - https://www.zhihu.com/question/20733617/answer/111577937  

      - https://blog.csdn.net/justloveyou_/article/details/62893086

  - Hashtable

    - https://blog.csdn.net/ns_code/article/details/36191279

    - Hashtable同样是基于哈希表实现的，同样每个元素是一个key-value对，其内部也是通过单链表解决冲突问题，容量不足（超过了阈值)时，同样会自动增长。

    - Hashtable也是JDK1.0引入的类，是线程安全的，能用于多线程环境中。

    - Hashtable同样实现了Serializable接口，它支持序列化，实现了Cloneable接口，能被克隆。

    - Hashtable#Entry

  - LinkedHashMap（底层是(数组+链表/红黑树)+环形双向链表，继承自HashMap)

  - TreeMap（底层是红黑树)

  - ConcurrentHashMap（底层是数组+链表/红黑树，基于CAS+synchronized)

    - http://www.importnew.com/21781.html

    - https://blog.csdn.net/dingji_ping/article/details/51005799

    - https://www.cnblogs.com/chengxiao/p/6842045.html

    - [http://ifeve.com/hashmap-concurrenthashmap-%E7%9B%B8%E4%BF%A1%E7%9C%8B%E5%AE%8C%E8%BF%99%E7%AF%87%E6%B2%A1%E4%BA%BA%E8%83%BD%E9%9A%BE%E4%BD%8F%E4%BD%A0%EF%BC%81/](http://ifeve.com/hashmap-concurrenthashmap-相信看完这篇没人能难住你！/)

- 分段锁实现

  - 采用 Segment + HashEntry的方式进行实现

- ConcurrentSkipListMap

  - ConcurrentSkipListMap有几个ConcurrentHashMap 不能比拟的优点：

  - 1、ConcurrentSkipListMap 的key是有序的。

  - 2、ConcurrentSkipListMap 支持更高的并发。ConcurrentSkipListMap 的存取时间是log（N)，和线程数几乎无关。也就是说在数据量一定的情况下，并发的线程越多，ConcurrentSkipListMap越能体现出他的优势。

  - SkipList 跳表：

  - 跳表是平衡树的一种替代的数据结构，但是和红黑树不相同的是，跳表对于树的平衡的实现是基于一种随机化的算法的，这样也就是说跳表的插入和删除的工作是比较简单的。

  - 下面来研究一下跳表的核心思想：

    - 先从链表开始，如果是一个简单的链表，那么我们知道在链表中查找一个元素I的话，需要将整个链表遍历一次。

    - 如果是说链表是排序的，并且节点中还存储了指向前面第二个节点的指针的话，那么在查找一个节点时，仅仅需要遍历N/2个节点即可。

    - 这基本上就是跳表的核心思想，其实也是一种通过“空间来换取时间”的一个算法，通过在每个节点中增加了向前的指针，从而提升查找的效率。

- Map实现类之间的区别

  - HashMap与ConcurrentHashMap区别

    - 1)前者允许key或value为null，后者不允许

    - 2)前者不是线程安全的，后者是

  - HashMap、TreeMap与LinkedHashMap区别

    - HashMap遍历时，取得数据的顺序是完全随机的；

    - TreeMap可以按照自然顺序或Comparator排序；

      - LinkedHashMap可以按照插入顺序或访问顺序排序，且get的效率（O(1))比TreeMap（O(logn))更高。

      - 2)HashMap底层基于哈希表，数组+链表/红黑树；

    - TreeMap底层基于红黑树

    - LinkedHashMap底层基于HashMap与环形双向链表
      - 3)就get和put效率而言，HashMap是最高的，LinkedHashMap次之，TreeMap最次。

  - ConcurrentHashMap、Collections.synchronizedMap与Hashtable的异同

    - 它们都是同步Map，但三者实现同步的机制不同；后两者都是简单地在方法上加synchronized实现的，锁的粒度较大；前者是基于CAS和synchronized实现的，锁的粒度较小，大部分都是lock-free无锁实现同步的。

    - ConcurrentHashMap还提供了putIfAbsent同步方法。

- Fail-Fast

  - 在ArrayList,LinkedList,HashMap等等的内部实现增，删，改中我们总能看到modCount的身影，modCount字面意思就是修改次数，但为什么要记录modCount的修改次数呢？ 

  - 所有使用modCount属性集合的都是线程不安全的。

  - 在一个迭代器初始的时候会赋予它调用这个迭代器的对象的modCount，在迭代器遍历的过程中，一旦发现这个对象的modCount和迭代器中存储的modCount不一样那就抛异常。

  - 它是 java 集合的一种错误检测机制，当多个线程对集合进行结构上的改变的操作时，有可能会产生 fail-fast。
    - 例如 ：假设存在两个线程（线程 1、线程 2)，线程 1 通过 Iterator 在遍历集合 A 中的元素，在某个时候线程 2 修改了集合 A 的结构（是结构上面的修改，而不是简单的修改集合元素的内容)，那么这个时候程序就会抛出 ConcurrentModificationException 异常，从而产生 fail-fast 机制。

  - 原因： 迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变 modCount 的值。

  - 每当迭代器使用 hashNext()/next() 遍历下一个元素之前，都会检测 modCount 变量是否为 expectedmodCount 值，是的话就返回遍历；否则抛出异常，终止遍历。

  - 解决办法：使用线程安全的集合