kafka架构

> ![image-20250915113757707](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250915-113809-image-20250915113757707.png)
>
> ![image-20250915113819873](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250915-113823-image-20250915113819873.png)

#### 基本概念

> - 基本组成
>
>   - 一个典型的kafka体系架构，主要包括
>
>     - 若干个producer
>       - 将消息发送到broker(经纪人)
>
>     - 若干个consumer
>       - 从broker订阅并且消费消息
>
>     - 若干个broker
>       - 将消息存储在磁盘上
>
>     - zookeeper集群
>       - 主要用来负责集群的元数据管理，控制器的选举等操作，（新版本已经不需要kafka）
>
> - 重要概念
>
>   - Topic
>
>   - partition
>
>     - 也叫做主题分区，同一个主题下的不同分区包含的消息不同
>
>     - 分区在存储层面可以看成一个可以追加的日志文件，消息在被追加到分区日志文件的时候会分配一个特定的偏移量（offset）offset是消息在分区中的唯一标识，kafka用他来保证消息在分区的顺序性，
>     - offset类似数据库的rowID， 只保证分区有序，不保证topic有序，所以如果想要保证有序，申请一个分区，但是性能下降，
>
>   - kafka为分区引入了多副本的机制(Replica) 
>     - 副本之间是一主多从的机制，leader负责读写，副本只负责和leader同步，leader宕机，可以从新选举，实现故障转移
>
>   - 消费端也具备了一定的容灾能力
>     - Consumer使用pull模式从服务端拉去消息，保证消费的具体位置，当消费者宕机后可以继续从上次的位置继续消费。所以可以保证消息不回丢失

##### kafka消息丢失的问题

> - kafka消息丢失问题
>
>   - kafka架构
>
>     <img src="https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250915-113657-8209378_e070813e-f264-4d74-f73c-500096e67ae8.png" alt="img" style="zoom:67%;" />
>
> - 消息传递语义
>
>   - at most once：最多一次。消息可能丢失也可能被处理，但最多只会被处理一次。
>
>   - at least once：至少一次。消息不会丢失，但可能被处理多次。可能重复，不会丢失。
>
>   - exactly once：精确传递一次。消息被处理且只会被处理一次。不丢失不重复就一次。
>
> - Kafka有三次消息传递的过程：
>
>   - 生产者发消息给Kafka Broker。
>
>   - Kafka Broker 消息同步和持久化
>
>   - Kafka Broker 将消息传递给消费者。
>
> - 生产者丢失消息
>
>   - 生产者发送消息流程
>
>     - 生产者是与leader直接交互，所以先从集群获取topic对应分区的leader元数据；
>
>     - 获取到leader分区元数据后直接将消息发给过去；
>
>     - Kafka Broker对应的leader分区收到消息后写入文件持久化；
>
>     - Follower拉取Leader消息与Leader的数据保持一致；
>
>     - Follower消息拉取完毕需要给Leader回复ACK确认消息；
>
>     - Kafka Leader和Follower分区同步完，Leader分区会给生产者回复ACK确认消息。
>
>   - 生产者采用push模式将数据发布到broker，每条消息追加到分区中，顺序写入磁盘。消息写入Leader后，Follower是主动与Leader进行同步。Kafka消息发送有两种方式：同步（sync）和异步（async），默认是同步方式，可通过producer.type属性进行配置。
>
>   - Kafka通过配置request.required.acks属性来确认消息的生产：
>
>     - 0 表示不进行消息接收是否成功的确认；不能保证消息是否发送成功，生成环境基本不会用。
>
>     - 1 表示当Leader接收成功时确认；只要Leader存活就可以保证不丢失，保证了吞吐量。
>
>     - -1 或者all表示Leader和Follower都接收成功时确认；可以最大限度保证消息不丢失，但是吞吐量低。
>
>   - 如果acks配置为0，发生网络抖动消息丢了，生产者不校验ACK自然就不知道丢了。
>
>   - 如果acks配置为1保证leader不丢，但是如果leader挂了，恰好选了一个没有ACK的follower，那也丢了。
>
>   - all：保证leader和follower不丢，但是如果网络拥塞，没有收到ACK，会有重复发的问题。
>
> - Kafka Broker丢失消息
>
>   - Kafka通过多分区多副本机制中已经能最大限度保证数据不会丢失，如果数据已经写入系统 cache 中但是还没来得及刷入磁盘，此时突然机器宕机或者掉电那就丢了，当然这种情况很极端。
>
> - 消费者丢失消息
>
> - 消费者通过pull模式主动的去 kafka 集群拉取消息，与producer相同的是，消费者在拉取消息的时候也是找leader分区去拉取。
>
> - 多个消费者可以组成一个消费者组（consumer group），每个消费者组都有一个组id。同一个消费组者的消费者可以消费同一topic下不同分区的数据，但是不会出现多个消费者消费同一分区的数据。
>
> - 消费者消费的进度通过offset保存在kafka集群的__consumer_offsets这个topic中。
>
> - 丢失场景
>
>   - 场景一：先commit再处理消息。如果在处理消息的时候异常了，但是offset 已经提交了，这条消息对于该消费者来说就是丢失了，再也不会消费到了。
>
>   - 场景二：先处理消息再commit。如果在commit之前发生异常，下次还会消费到该消息，重复消费的问题可以通过业务保证消息幂等性来解决。

