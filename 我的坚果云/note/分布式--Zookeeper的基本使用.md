- https://www.runoob.com/w3cnote/zookeeper-tutorial.html

- ZooKeeper 是一个分布式的，开放源码的分布式应用程序协同服务

- 使用场景

  - 配置管理（configuration management）：如果我们做普通的 Java 应用，一般配置项就是一个本地的配置文件，如果是微服务系统，各个独立服务都要使用集中化的配置管理，这个时候就需要 ZooKeeper。

  - DNS 服务

  - 组成员管理（group membership）：比如上面讲到的 HBase 其实就是用来做集群的组成员管理。

  - 各种分布式锁

- ZooKeeper 使用文件系统模型主要基于以下两点考虑：

  - 文件系统的树形结构便于表达数据之间的层次关系。

  - 文件系统的树形结构便于为不同的应用分配独立的命名空间（namespace）。

- 使用和配置

  - 把 conf 目录下的 zoo_sample.cfg 重命名为 zoo.cfg，然后修改配置。

    ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250927-132421-8209378_9378d560-1f3d-410f-ee63-83a6cb795a35.png)

    ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250927-204153-8209378_5d58e244-ac0c-4579-f789-f7d1512d94c5.png)

  - zookeeper 的三个端口作用

    - 1、2181 : 对 client 端提供服务

    - 2、2888 : 集群内机器通信使用

    - 3、3888 : 选举 leader 使用

- 二、客户端的curator连接

  - Curator 是 Netflix 公司开源的一套 zookeeper 客户端框架，解决了很多 Zookeeper 客户端非常底层的细节开发工作，包括连接重连、反复注册 Watcher 和 NodeExistsException 异常等。

    - curator-framework：对 zookeeper 的底层 api 的一些封装。

    - curator-client：提供一些客户端的操作，例如重试策略等。

    - curator-recipes：封装了一些高级特性，如：Cache 事件监听、选举、分布式锁、分布式计数器、分布式 Barrier 等。

- Zookeeper 选举机制

  - 个人能力

  - 遇强改投

  - 投票箱

  - 领导者

- 什么场景下 Zookeeper 需要选举？

  - （1）服务器初始化启动。

  - （2）服务器运行期间 Leader 故障。

- 运行时期的Leader选举

  - 主服务器宕机才会触发选举，有新的加入不会

- 选举机制中涉及到的核心概念

  - （1）Server id（或sid）：服务器ID
    - 编号越大在选择算法中的权重越大，比如初始化启动时就是根据服务器ID进行比较。

  - （2）Zxid：事务ID
    - 服务器中存放的数据的事务ID，值越大说明数据越新，在选举算法中数据越新权重越大。

  - （3）Epoch：逻辑时钟
    - 也叫投票的次数，同一轮投票过程中的逻辑时钟值是相同的，每投完一次票这个数据就会增加。

  - （4）Server状态：选举状态

    - LOOKING，竞选状态。

    - FOLLOWING，随从状态，同步leader状态，参与投票。

    - OBSERVING，观察状态,同步leader状态，不参与投票。

    - LEADING，领导者状态。

- 参考

  - zookeeper
    - [https://www.cnblogs.com/blackbinbin/p/17067519.html](https://www.cnblogs.com/blackbinbin/p/17067519.html)