> - 为啥mysql选择b+树来存储索引
>
>   - B+树牺牲非叶子节点存储键值，换取了更矮的树高，减少了磁盘IO的次数。
>
>   - 并且B+树对于范围查询更好，因为是有序的双向链表，只需定位到起始点，然后顺序扫描叶子节点链表即可，效率非常高（接近O(n)的线性扫描）。B树进行范围查询则需要不断地在树的不同层级间回溯，效率低得多。
>
>   - 全表扫描更快，只需要遍历叶子节点即可
>
>   - 更适合磁盘预读特性： 磁盘读写有一个“局部性原理”，即读取一个数据块时，通常会将该块附近的数据也读入内存（预读）。B+树的范围查询和顺序扫描能很好地利用这种预读，连续读取的叶子节点有很大概率已经在内存中。B树的不规则访问模式则较难充分利用预读。
>
> - mysql单表数据量多少通常需要分表
>
>   - 通常要考虑具体的业务场景，阿里给的是500万，
>
> - mysql磁盘页和叶子节点什么关系
>
>   - MySQL 中的磁盘页（Disk Page） 和 B+ 树的叶子节点（Leaf Node） 本质上是 同一事物的两种视角：
>
>   - 磁盘页是物理存储的最小单元，叶子节点是 B+ 树逻辑结构在物理页上的具体组织形式。
>
> - mysql 如何支持范围查找走索引
>
>   - 先定位到边界，因为是有序链表，然后顺序扫描槽位对应的记录
>
> - 什么是索引下推
>
>   - 索引下推（Index Condition Pushdown，ICP） 是 MySQL 5.6 引入的核心优化技术，它通过改变查询执行流程，将部分 WHERE 条件的过滤操作从 Server 层 下推到 存储引擎层 执行，从而显著减少回表次数和磁盘 I/O。
>
> - 范围查找索引失效原理解析
>
>   - 可能由于mysql底层优化发现走索引比不走索引还要慢，比如回表次数过多，那么就会不走索引
>
> - order by为啥会导致索引失效
>
>   - 因为orderby 如果按照索引排序，但是select * ，那么会导致回表，所以mysql底层会优化，直接全表扫描的速度可能更快
>
> - 对字段进行操作导致索引失效原理
>
>   - 比如where a =1, 因为a是varchar类型，执行这个之前，会先把a 转换成int类型，然后就会全表执行。
>
>   - 比如 where a+1 =1 ,对字段进行操作了，也会导致索引失效
>
> - java guade  
>
>   - [https://javaguide.cn/database/mysql/mysql-questions-01.html](https://javaguide.cn/database/mysql/mysql-questions-01.html)
>
>     - [CHAR 和 VARCHAR 的区别是什么？](https://javaguide.cn/database/mysql/mysql-questions-01.html#char-和-varchar-的区别是什么) 一个可变长，一个不可变，一个存储需要多俩个字节存储长度信息，一般用来存储不可变长的字段
>
>     - [VARCHAR(100)和 VARCHAR(10)的区别是什么？](https://javaguide.cn/database/mysql/mysql-questions-01.html#varchar-100-和-varchar-10-的区别是什么)
>
>     - [为什么不推荐使用 TEXT 和 BLOB？](https://javaguide.cn/database/mysql/mysql-questions-01.html#为什么不推荐使用-text-和-blob)
>
>       - 不能有默认值。
>
>       - 在使用临时表时无法使用内存临时表，只能在磁盘上创建临时表（《高性能 MySQL》书中有提到）。
>
>       - 检索效率较低。
>
>       - 不能直接创建索引，需要指定前缀长度。
>
>       - 可能会消耗大量的网络和 IO 带宽。
>
>       - 可能导致表上的 DML 操作变慢。
>
>       - 著作权归JavaGuide(javaguide.cn)所有基于MIT协议原文链接：[https://javaguide.cn/database/mysql/mysql-questions-01.html](https://javaguide.cn/database/mysql/mysql-questions-01.html)
>
>     - [DATETIME 和 TIMESTAMP 的区别是什么？](https://javaguide.cn/database/mysql/mysql-questions-01.html#datetime-和-timestamp-的区别是什么)
>       - DATETIME 类型没有时区信息，TIMESTAMP 和时区有关。
>
>     - [MySQL 基础架构](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-基础架构)
>
>       - service层
>
>         - 连接器
>
>         - 查询器
>
>         - 优化器
>
>         - 执行器
>
>       - 存储引擎层
>
>   - [https://segmentfault.com/a/1190000040129107](https://segmentfault.com/a/1190000040129107)
>
>   - [MySQL next-key lock 加锁范围是什么？](https://segmentfault.com/a/1190000040129107)
>
>   - [https://zhuanlan.zhihu.com/p/286830069](https://zhuanlan.zhihu.com/p/286830069)
>
>     - 索引不适合哪些场景
>
>       - 数据量少的不适合加索引
>
>       - 更新比较频繁的也不适合加索引
>
>       - 区分度低的字段不适合加索引（如性别）
>
>     - 索引的一些潜规则
>
>       - 覆盖索引
>
>       - 回表
>
>       - 索引数据结构（B+树）
>
>       - 最左前缀原则
>
>       - 索引下推
>
>     - \2. MySQL 遇到过死锁问题吗，你是如何解决的？
>
>       - [https://juejin.cn/post/6844904121758138382](https://juejin.cn/post/6844904121758138382)
>         - 这时候可以用select * from information_schema.innodb_locks;查看锁情况：
>
>       - [https://mp.weixin.qq.com/s?__biz=MzkyMzU5Mzk1NQ==&mid=2247505902&idx=1&sn=beac1995d53b16a241cc3fc5bd3d04d6&source=41&poc_token=HE-6lWij67PiBVZ1zmCAagCq9gPIB9oM-mzijpA](https://mp.weixin.qq.com/s?__biz=MzkyMzU5Mzk1NQ==&mid=2247505902&idx=1&sn=beac1995d53b16a241cc3fc5bd3d04d6&source=41&poc_token=HE-6lWij67PiBVZ1zmCAagCq9gPIB9oM-mzijpA)_
>
>     - 索引优化20条 [https://juejin.cn/post/6844904098999828488](https://juejin.cn/post/6844904098999828488)
>
>       - 1、查询SQL尽量不要使用select *，而是select具体字段。
>
>       - 2、如果知道查询结果只有一条或者只要最大/最小一条记录，建议用limit 1
>
>       - 3、应尽量避免在where子句中使用or来连接条件 。union all 
>
>       - 4、优化limit分页
>
>         - 返回上次最大ID
>
>         - 先查询ID，避免回表
>
>       - 5、优化你的like语句
>
>       - 6、使用where条件限定要查询的数据，避免返回多余的行
>
>       - 7、尽量避免在索引列上使用mysql的内置函数
>
>       - 8、应尽量避免在 where 子句中对字段进行表达式操作，这将导致系统放弃使用索引而进行全表扫
>
>       - 9、Inner join 、left join、right join，优先使用Inner join，如果是left join，左边表结果尽量小
>
>       - 10、应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描。
>
>       - 16、删除冗余和重复索引
>
>       - 22、索引不宜太多，一般5个以内。
>
>     - [https://juejin.cn/post/6844904115353436174#heading-8](https://juejin.cn/post/6844904115353436174#heading-8)
>
>   - sql为什么慢
>
>     - 没有走索引
>
>     - 回表次数过多
>
>     - IO次数过多

