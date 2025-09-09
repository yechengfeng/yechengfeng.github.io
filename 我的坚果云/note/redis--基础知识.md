1. ### redis相关资料

   1. https://open8gu.com/redis/persistent/fznigr883bhhbp0m/	
   2. https://www.xiaolincoding.com/redis/

2. #### redis简介

   > 1. Redis 是一种基于内存的数据库，对数据的读写操作都是在内存中完成，因此读写速度非常快，常用于缓存，消息队列、分布式锁等场景。
   > 2. 并且对数据类型的操作都是原子性的，因为执行命令由单线程负责的，不存在并发竞争的问题。除此之外，Redis 还支持事务、持久化、Lua 脚本、多种集群方案（主从复制模式、哨兵模式、切片机群模式）、发布/订阅模式，内存淘汰机制、过期删除机制等等。
   > 3. Redis 和 Memcached 有什么区别？
   >    1. Redis 支持的**数据类型**更丰富 (String、Hash、List、Set、ZSet)，而 Memcached 只支持最简单的 key-value 数据类型；
   >    2. Redis 支持**数据的持久化**，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用，而 Merncached 没有持久化功能，数据全部存在内存之中，Memcached 重启或者挂掉后，数据就没了；
   >    3. Redis 原生支持集群模式，Memcached 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；
   >    4. Redis 支持发布订阅模型、Lu日脚本、事务等功能，而Memcached 不支持；

3. ### redis数据类型应用场景

   1. **String 类型的应用场景：缓存对象、常规计数、分布式锁、共享 session 信息等。**

      1. 常用命令 ：get/set/del/incr/decr/incrby/decrby

   2. **List 类型的应用场景：消息队列 （但是有两个问题：1.生产者需要自行实现全局唯一ID；2.不能以消费组形式消费数据）等。参考 https://www.51cto.com/article/640335.html**

      1. 定时排行榜
         - **list类型的lrange命令可以分页查看队列中的数据。可将每隔一段时间计算一次的排行榜存储在list类型中，如QQ音乐内地排行榜，每周计算一次存储再list类型中，访问接口时通过page和size分页转化成lrange命令获取排行榜数据。但是，并不是所有的排行榜都能用list类型实现，只有定时计算的排行榜才适合使用list类型存储，与定时计算的排行榜相对应的是实时计算的排行榜，list类型不能支持实时计算的排行榜，下面介绍有序集合sorted set的应用场景时会详细介绍实时计算的排行榜的实现。**

   3. **Hash 类型：缓存对象、购物车等。**

   4. **set 类型：聚合计算（并集、交集、差集）场景，比如点赞、共同关注、抽奖活动等。**

      1. 实战场景：收藏夹

         - 例如QQ音乐中如果你喜欢一首歌，点个『喜欢』就会将歌曲放到个人收藏夹中，每一个用户做一个收藏的集合，每个收藏的集合存放用户收藏过的歌曲id。

         - key为用户id，value为歌曲id的集合

   5. **zset 类型：排序场景，比如排行榜、电话和姓名排序等。**

      - 有序集合的特点是有序，无重复值。与set不同的是sorted set每个元素都会关联一个score属性，redis正是通过score来为集合中的成员进行从小到大的排序。
      - 实战场景：实时排行榜
        - QQ音乐中有多种实时榜单，比如飙升榜、热歌榜、新歌榜，可以用redis key存储榜单类型，score为点击量，value为歌曲id，用户每点击一首歌曲会更新redis数据，sorted set会依据score即点击量将歌曲id排序。

   6. **BitMap （2.2 版新增)：二值状态统计的场景，比如签到、判断用户登陆状态、连续签到用户总数等 •**

   7. **HyperLogLog （2.8 版新增)：海量数据基数统计的场景，比如百万级网页 UV计数等；https://www.cnblogs.com/54chensongxia/p/13803465.html**

   8. **GEO （3.2版新增）：存储地理位置信息的场景，比如滴滴叫车；**

   9. **Stream（5.0版新增)：消息队列，相比于基于 List 类型实现的消息队列，有这两个特有的特性：自动生成全局唯一消息ID，支持以消费组形式消费数据。**

4. ### redis数据结构如何实现的

   1. https://www.cnblogs.com/ysocean/p/9102811.html

5. ### redis的线程模型

   1. 可以谈谈单线程，IO多路复用
      1. Redis 采用了 10 多路复用机制处理大量的客户端 Socket 请求，10 多路复用机制是指一个线程处理多个10流，就是我们经常听到的select/epoll 机制。简单来说，在Redis 只运行单线程的情况下，该机制允许内核中，同时存在多个监听 Socket 和已连接 Socket。内核会一直监听这些 Socket 上的连接请求或数据请求。一旦有请求到达，就会交给 Redis 线程处理，这就实现了一个 Redis 线程处理多个10 流的效果。

6. ### Redis 6.0 之后为什么引入了多线程？

   1. 多线程主要引用在了IO处理上面， 如果需要配置 多线程 则  配置文件中

      - io-threads-do-reads yes

      - io-threads 4

   2. 关于线程数的设置，官方的建议是如果为 4核的CPU，建议线程数设置为 2 或了，如果为8核CPU建议线程数设置为 6，线程数一定要小于机器核数，线程数并不是越大越好。

   3. 因此，Redis 6.0 版本之后，Redis 在启动的时候，默认情况下会额外创建 6 个线程（这里的线程数不包括主线程）：

   4. Redis-server ： Redis的主线程，主要负责执行命令；

   5. bio_close_file bio_aof_fsync、bio_ lazy_free：三个后台线程，分别异步处理关闭文件任务、AOF刷盘任务、释放内存任务；

   6. • io_thd 1、 io thd_ 2、io_thd_3：三个io线程，io-threads 默认是 4，所以会启动3 （4-1）个10多线程，用来分担 Redis 网络io的压力。

7. ### redis的持久化

   1. Redis 共有三种数据持久化的方式：

      - AOF 日志：每执行一条写操作命令，就把该命令以追加的方式写入到一个文件里；

      - RDB 快照：将某一时刻的内存数据，以二进制的方式写入磁盘；

      - 混合持久化方式：Redis 4.0 新增的方式，集成了 AOF 和 RDB 的优点；

   2. AOF 日志是如何实现的？

      - https://open8gu.com/redis/persistent/by6eqgg5fv4roz1r/

   3. RDB是如何实现的

      - https://open8gu.com/redis/persistent/fznigr883bhhbp0m/

   4. 为什么会有混合持久化？

8. ### [redis集群](https://www.xiaolincoding.com/redis/base/redis_interview.html#redis-如何实现服务高可用)  

   - https://zhuanlan.zhihu.com/p/387630094

   - 要想设计一个高可用的 Redis 服务，一定要从 Redis 的多服务节点来考虑，比如Redis 的主从复制、哨兵模式、切片集群

   - 主从复制

     - 主从复制是 Redis 高可用服务的最基础的保证，实现方案就是将从前的一台 Redis 服务器，同步数据到多台从Redis 服务器上，即一主多从的模式，且主从服务器之间采用的是「读写分离」的方式。

       - 客户端从主服务器读写，从服务器只读，主服务器异步写入从服务器，所以数据不一致是难免的

       - 生产上永远不可能用单机，所有的节点，包括应用节点，中间件节点，数据节点等都要保证高可用，最基本的要求。

       - 主从必然存在延迟问题，Redis按照分布式集群来讲，就不是强一致性的[CAP结构](https://zhida.zhihu.com/search?content_id=174270458&content_type=Article&match_order=1&q=CAP结构&zhida_source=entity)，只保证了高可用AP，没有保证强一致性CP，所以必然存在延迟。

       - CAP：

         - 一致性(Consistency)

         - 可用性(Availability)

         - 分区容忍性(Partition tolerance)

       - 如果业务需要主从机制必须强一致，那就要考虑适不适合选择Redis。

9. ### redis数据结构之跳表

   - https://redisbook.readthedocs.io/en/latest/internal-datastruct/skiplist.html 

   - https://www.bilibili.com/video/BV1LiTczAELp/?spm_id_from=333.337.search-card.all.click&vd_source=8db2bf2ffb3ab22965fccab48d4389ba

   - 跳表就是在链表的基础上增加了多级索引，Zset的数据结构

   - List的数据结构是  quickList  ，将双向链表和压缩列表结合。既实现了链表的操作灵活，又实现了压缩列表的内存优势

10. ### redis-消息队列

    1. https://segmentfault.com/a/1190000012244418

11. ### redis的内存淘汰策略

    1. https://zhuanlan.zhihu.com/p/105587132

12. ### 常用命令和数据结构

    1.  String字符串

       - `SET KEY_NAME VALUE`

       - string类型是Redis最基本的数据类型，一个键最大能存储512MB。

    2.  Hash哈希

       - `HSET KEY_NAME FIELD VALUE`

       - Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。
       - **当对象的某个属性需要频繁修改时，不适合用string+json，因为它不够灵活，每次修改都需要重新将整个对象序列化并赋值；如果使用hash类型，则可以针对某个属性单独修改，没有序列化，也不需要修改整个对象。比如，商品的价格、销量、关注数、评价数等可能经常发生变化的属性，就适合存储在hash类型里。**

    3.  List列表

       - `LPUSH KEY_NAME VALUE1.. VALUEN`
         - 在 key 对应 list 的头部添加字符串元素

       - `RPUSH KEY_NAME VALUE1..VALUEN`
         - 在 key 对应 list 的尾部添加字符串元素
       - `LREM KEY_NAME COUNT VALUE`
         - 对应 list 中删除 count 个和 value 相同的元素

       - `LLEN KEY_NAME `
         - 返回 key 对应 list 的长度

    4. Set集合

       - `SADD KEY_NAME VALUE1...VALUEn`
         - 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。

    5. Sorted Set有序集合

       - `ZADD KEY_NAME SCORE1 VALUE1.. SCOREN VALUEN`

         - edis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。

         - redis正是通过分数来为集合中的成员进行从小到大的排序。

         - zset的成员是唯一的,但分数(score)却可以重复。

    6. Redis常用命令参考
       - https://www.redis.net.cn/order/

13. ### Redis事务机制

    -  Redis事务生命周期

      - 开启事务：使用MULTI开启一个事务

      - 命令入队列：每次操作的命令都会加入到一个队列中，但命令此时不会真正被执行

      - 提交事务：使用EXEC命令提交事务，开始顺序执行队列中的命令

    - Redis事务到底是不是原子性的？
      - 官方认为Redis事务是一个原子操作，这是站在执行与否的角度考虑的。但是从ACID原子性定义来看，严格意义上讲Redis事务是非原子型的，因为在命令顺序执行过程中，一旦发生命令执行错误Redis是不会停止执行然后回滚数据。

    - Redis为什么不支持回滚（roll back）？
      - 支持事务回滚能力会导致设计复杂，这与Redis的初衷相违背，通常只有命令错误才会发生执行错误，一般不会出现在生产环境

    - Redis事务相关命令

      - （1）WATCH
        - 可以为Redis事务提供 check-and-set （CAS）行为。被WATCH的键会被监视，并会发觉这些键是否被改动过了。 如果有至少一个被监视的键在 EXEC 执行之前被修改了， 那么整个事务都会被取消， EXEC 返回nil-reply来表示事务已经失败。

      - （2）MULTI
        - 用于开启一个事务，它总是返回OK。MULTI执行之后,客户端可以继续向服务器发送任意多条命令， 这些命令不会立即被执行，而是被放到一个队列中，当 EXEC命令被调用时， 所有队列中的命令才会被执行。

      - （3）UNWATCH
        - 取消 WATCH 命令对所有 key 的监视，一般用于DISCARD和EXEC命令之前。如果在执行 WATCH 命令之后， EXEC 命令或 DISCARD 命令先被执行了的话，那么就不需要再执行 UNWATCH 了。因为 EXEC 命令会执行事务，因此 WATCH 命令的效果已经产生了；而 DISCARD 命令在取消事务的同时也会取消所有对 key 的监视，因此这两个命令执行之后，就没有必要执行 UNWATCH 了。

      - （4）DISCARD
        - 当执行 DISCARD 命令时， 事务会被放弃， 事务队列会被清空，并且客户端会从事务状态中退出。

      - （5）EXEC

        - 负责触发并执行事务中的所有命令：

        - 如果客户端成功开启事务后执行EXEC，那么事务中的所有命令都会被执行。

        - 如果客户端在使用MULTI开启了事务后，却因为断线而没有成功执行EXEC,那么事务中的所有命令都不会被执行。需要特别注意的是：即使事务中有某条/某些命令执行失败了，事务队列中的其他命令仍然会继续执行，Redis不会停止执行事务中的命令，而不会像我们通常使用的关系型数据库一样进行回滚。

14. ### 主从复制/哨兵/集群

1. ##### **主从复制（数据是同步的，类似于MySQL Replication)**

   - 1、slave与master建立连接，发送sync同步命令（slaveof命令)

   - 2、master会开启一个后台进程，将数据库快照保存到文件中（bgsave)，同时master主进程会开始收集新的写命令并缓存到backlog队列

   - 3、后台进程完成保存后，就将文件发送slave

   - 4、slave会丢弃所有旧数据，开始载入master发来的快照文件

   - 5、master向slave发送存储在backlog队列中的写命令，发送完毕后，每执行一个写命令，就向slave发送相同的写命令（异步复制)

   - 6、slave执行master发来的所有存储在缓冲区的写命令，并从现在开始，接收并执行master传来的每个写命令

   - 在接收到master发送的数据初始副本之后，客户端 向master写入时，slave都会实时得到更新。在部署好slave之后，客户端就可以向任意一个slave发送读请求了，而不必总是把读请求发送给master。（负载均衡)

   - 注意，快照指的就是RDB方式。

   - slave是不能写的，是只读的；只有master可以写。

2. ##### **哨兵 sentinel（数据是同步的)**

   - 既然主从模式中，当master节点挂了以后，slave节点不能主动选举一个master节点出来，那么我就安排一个或多个Sentinal来做这件事，当Sentinal发现master节点挂了以后，Sentinal就会从slave中重新选举一个master。

   - 对Sentinal模式的理解：

   - Sentinal模式是建立在主从模式的基础上，如果只有一个Redis节点，Sentinal就没有任何意义

   - 当master节点挂了以后，Sentinal会在slave中选择一个作为master，并修改它们的配置文件，其他slave的配置文件也会被修改，比如slaveof属性会指向新的master

   - 当master节点重新启动后，它将不再是master而是作为slave接收新的master节点的同步数据

   - Sentinal因为也是一个进程有挂掉的可能，所以Sentinal也会启动多个形成一个Sentinal集群

   - 当主从模式配置密码时，Sentinal也会同步将配置信息修改到配置文件中，不许要担心。

   - 一个Sentinal或Sentinal集群可以管理多个主从Redis。

   - Sentinal最好不要和Redis部署在同一台机器，不然Redis的服务器挂了以后，Sentinal也挂了。

   - 当使用Sentinal模式的时候，客户端就不要直接连接Redis，而是连接Sentinal的ip和port，由Sentinal来提供具体的可提供服务的Redis实现，这样当master节点挂掉以后，Sentinal就会感知并将新的master节点提供给使用者。

   - Sentinal模式基本可以满足一般生产的需求，具备高可用性。但是当数据量过大到一台服务器存放不下的情况时，主从模式或Sentinal模式就不能满足需求了，这个时候需要对存储的数据进行分片，将数据存储到多个Redis实例中，就是下面要讲的。

3. ##### 原理

   - ①sentinel集群通过给定的配置文件发现master，启动时会监控master。通过向master发送info信息获得该服务器下面的所有从服务器。

   - ②sentinel集群通过命令连接向被监视的主从服务器发送hello信息(每秒一次)，该信息包括sentinel本身的ip、端口、id等内容，以此来向其他sentinel宣告自己的存在。

   - ③sentinel集群通过订阅连接接收其他sentinel发送的hello信息，以此来发现监视同一个主服务器的其他sentinel；集群之间会互相创建命令连接用于通信，因为已经有主从服务器作为发送和接收hello信息的中介，sentinel之间不会创建订阅连接。

   - ④sentinel集群使用ping命令来检测实例的状态，如果在指定的时间内（down-after-milliseconds)没有回复或则返回错误的回复，那么该实例被判为下线。 

   - ⑤当failover主备切换被触发后，failover并不会马上进行，还需要sentinel中的大多数sentinel授权后才可以进行failover，即进行failover的sentinel会去获得指定quorum个的sentinel的授权，成功后进入ODOWN状态。如在5个sentinel中配置了2个quorum，等到2个sentinel认为master死了就执行failover。

   - ⑥sentinel向选为master的slave发送SLAVEOF NO ONE命令，选择slave的条件是sentinel首先会根据slaves的优先级来进行排序，优先级越小排名越靠前。如果优先级相同，则查看复制的下标，哪个从master接收的复制数据多，哪个就靠前。如果优先级和下标都相同，就选择进程ID较小的。

   - ⑦sentinel被授权后，它将会获得宕掉的master的一份最新配置版本号(config-epoch)，当failover执行结束以后，这个版本号将会被用于最新的配置，通过广播形式通知其它sentinel，其它的sentinel则更新对应master的配置。

   - ①到③是自动发现机制:

   - 以10秒一次的频率，向被监视的master发送info命令，根据回复获取master当前信息。

   - 以1秒一次的频率，向所有redis服务器、包含sentinel在内发送PING命令，通过回复判断服务器是否在线。

   - 以2秒一次的频率，通过向所有被监视的master，slave服务器发送当前sentinel，master信息的消息。

   - ④是检测机制，⑤和⑥是failover机制，⑦是更新配置机制。

4. ##### 集群（数据是分片的，sharing)

   - cluster的出现是为了解决单机Redis容量有限的问题，将Redis的数据根据一定的规则分配到多台机器。对cluster的一些理解：

   - cluster可以说是Sentinal和主从模式的结合体，通过cluster可以实现主从和master重选功能，所以如果配置两个副本三个分片的话，就需要六个Redis实例。

   - 配置集群需要至少3个主节点（每个主节点对应一个从节点，这样一共6个节点)

   - 因为Redis的数据是根据一定规则分配到cluster的不同机器的，当数据量过大时，可以新增机器进行扩容

   - 这种模式适合数据量巨大的缓存要求，当数据量不是很大使用Sentinal即可。

   - Redis 集群通过分区（partition)来提供一定程度的可用性（availability)： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

   - Redis 集群提供了以下两个好处：

   - 将数据自动切分（split)到多个节点的能力。

   - 当集群中的一部分节点失效或者无法进行通讯时， 仍然可以继续处理命令请求的能力。

   - 不同的master存放不同的数据，所有的master数据的并集是所有的数据。

   - master与之对应的slave数据是一样的。

5. ##### 数据分片

   - \1) Hash映射（并非一致性哈希，而是哈希槽)

     - Redis采用哈希槽（hash slot)的方式在服务器端进行分片。
       - HASH_SLOT = CRC16(key) mod 16384（2^14=16384)

     - 在redis官方给出的集群方案中，数据的分配是按照槽位来进行分配的，每一个数据的键被哈希函数映射到一个槽位，redis-3.0.0规定一共有16384个槽位，当然这个可以根据用户的喜好进行配置。当用户put或者是get一个数据的时候，首先会查找这个数据对应的槽位是多少，然后查找对应的节点，然后才把数据放入这个节点。这样就做到了把数据均匀的分配到集群中的每一个节点上，从而做到了每一个节点的负载均衡，充分发挥了集群的威力。

     - 计算key字符串对应的映射值，redis采用了crc16函数然后与0x3FFF取低16位的方法。crc16以及md5都是比较常用的根据key均匀的分配的函数，就这样，用户传入的一个key我们就映射到一个槽上，然后经过gossip协议，周期性的和集群中的其他节点交换信息，最终整个集群都会知道key在哪一个槽上。 

     - Redis 集群有16384个哈希槽,每个key通过CRC16校验后对16384取模来决定放置哪个槽.集群的每个节点负责一部分hash槽,举个例子,比如当前集群有3个节点,那么:

     - 节点 A 包含 0 到 5500号哈希槽.

     - 节点 B 包含5501 到 11000 号哈希槽.

     - 节点 C 包含11001 到 16384号哈希槽.

     - 这种结构很容易添加或者删除节点. 比如如果我想新添加个节点D, 我需要从节点 A, B, C中得部分槽到D上. 如果我想移除节点A,需要将A中的槽移到B和C节点上,然后将没有任何槽的A节点从集群中移除即可. 由于从一个节点将哈希槽移动到另一个节点并不会停止服务,所以无论添加删除或者改变某个节点的哈希槽的数量都不会造成集群不可用的状态.

     - 位序列结构

     - Master节点维护着一个16384/8字节的位序列，Master节点用bit来标识对于某个槽自己是否拥有。比如对于编号为1的槽，Master只要判断序列的第二位（索引从0开始)是不是为1即可。

   - \2) 范围映射

     - 范围映射通常选择key本身而非key的函数计算值来作为数据分布的条件，且每个数据节点存放的key的值域是连续的一段范围。

     - key的值域是业务层决定的，业务层需要清楚每个区间的范围和Redis实例数量，才能完整地描述数据分布。这使业务层的key值域与系统层的实例数量耦合，数据分片无法在纯系统层实现。

   - \3) Hash和范围结合

6. 节点间通信协议——Gossip

7. 主从选举——Raft

8. 功能限制

9. 数据迁移/在线扩容

10. 参考资料

    - https://www.xiaolincoding.com/redis/data_struct/data_struct.html 

    - https://javaguide.cn/database/redis/redis-data-structures-01.html

    - http://www.itsoku.com/course/1/201



### redis场景问题汇总

- ##### redis 记录上亿用户连续登录次数

  - Redis 提供的bit map实现
    - 如果用户量比较多的情况下，用日期作为key，用户ID作为比特位

- ##### 给你一个亿的redis key，统计双方的共同好友

  - SINTERSTORE 命令实现交集

  - 对于这个问题，Noe4j 天然支持数据关系的这种处理，neo4j支持10亿个节点

  - Hbase +hadoop

- ##### redis实现上亿用户的实时积分排行榜

  - zset实现排行榜

  - 对于这种数据量大的，可以根据value 放入不同的桶中

- ##### redis 当中的RDB 和AOF机制

  - RDB  : redis database的一个简写

    - 在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork—个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。

    - 性能高，主进程不需要进行IO操作

    - 备份方便，只包含一个dump.RDB

    - RDB是每隔一段时间进行持久化，如果持久化期间redis发生故障，会发生数据丢失，如果对于数据不严谨的情况下

    - RDB如果数据量大，导致真个服务停止几百毫秒 甚至1秒

  - AOF Append only file

    - 以日志的形式记录服务器所处理的每一个写、删除操作，奇询操作不会记录，以文本的方式记录，可以打开文件看到具体的操作记录

    - 数据安全 ，redis提供了三种同步策略
      - 每秒同步 每次修改同步，不同步

    - AOF 机制的rewrite 模式，定期对AOF文件进行重写，以达到压缩目的

    - 缺点

      - 文件打，回复速度比较慢

      - 运行效率没有RDB高

    - 比对

      - AOF文件比RDB更新频率高，优先使用AOF还原数据。

      - AOF比LRDB更安全也更大

      - RDB性能比AOF好

      - 如果两个都配了优先加载AOF

- ##### redis过期删除策略

  - 惰性删除 
    - 只有当访问一个key时，才会判断该key是否已过期，过期则清除。该策略可以最大化地节省CPU 资源，却对内存非常不友好。极端情况可能出现大量的过期key没有再次被访问，从而不会被清除，占用大量内存。

  - 定期删除
    - 每隔一定的时间，会扫描一定数量的数据库的expires字典中一定数量的key，并清除其中已过期的 key。该策略是前两者的一个折中方案。通过调整定时扫描的时间间隔和每次扫描的限定耗时，可以在不同情况下使得CPU和内存资源达到最优的平衡效果。

- ##### redis的分布式锁底层如何实现

  - 利用setNx来保证，如果key不存在才能获取到锁，如果存在获取不到锁

  - lua脚本来保证多个redis操作的原子性

  - 同时考虑锁过期，需要一个额外的看门狗定时任务来监听锁过期是否需要续约

  - 同时考虑到redis节点挂掉之后，采用红锁来同时向n/2+1个节点申请锁，都申请到了才能获取锁成功

- ##### redis主从复制的核心原理

- ##### redis 和mysql如何保证数据一致

  - \1. 先更新 mysql ，再更新redis。如果更新redis失败，还是可能不一致

  - \2. 先删除redis缓存数据，再更新mysql，再次查询的时候将数据添加到缓存中，可能导致数据不一致

  - 延迟双删:先删除redis数据 再更新mysql，延迟几百秒再删一次redis.

- ##### redis集群方案





