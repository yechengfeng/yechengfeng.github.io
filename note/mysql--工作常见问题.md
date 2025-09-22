参考 [https://www.xiaolincoding.com/mysql/base/how_select.html#mysql-%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E6%98%AF%E6%80%8E%E6%A0%B7%E7%9A%84](https://www.xiaolincoding.com/mysql/base/how_select.html#mysql-%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E6%98%AF%E6%80%8E%E6%A0%B7%E7%9A%84)

1. ##### mysql执行流程是什么样的

   > ![image-20250909161336285](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250909-161342-image-20250909161336285.png)

   1. ##### mysql的逻辑架构 

      1. **客户端** 
      2. **services**
         - 连接器--》查询缓存->解析器->优化器
      3. **存储引擎**

   2. ##### 优化和执行

      1. **mysql会解析查询，创建内部的数据结构（解析树）然后对其优化，包括重写查询，决定表的读取顺序，选择合适的索引，用户可以更具关键字提示优化器，影响他的决策过程**
      2. **查询缓存在mysql8.0已经禁止**
         1. 查询缓存的一个问题是收到单个互斥锁的保护，导致互斥锁争用，5.6也是默认关闭查询缓存的 query_cache_type =0
         2. 查询缓存使用一个全局锁（query_cache_lock）来管理缓存操作（检查、存储、失效）。在高并发环境下，这个全局锁成为巨大的瓶颈。即使查询本身很快，获取和释放这个锁的开销也会导致严重的性能下降。写操作（INSERT, UPDATE, DELETE, DDL）需要使涉及到的表的所有缓存查询失效（invalidate）。这个失效操作也需要持有全局锁，阻塞所有其他缓存操作（读和写），在高并发写或读写混合场景下，这会导致显著的延迟。

2. mysql一行记录是怎么存储的

   1. 先来看看 MySQL 数据库的文件存放在哪个目录？

      - ```sql
        - SHOW VARIABLES LIKE 'datadir';
        ```

        我们每创建一个 database（数据库） 都会在 /var/lib/mysql/ 目录里面创建一个以 database 为名的目录，然后保存表结构和表数据的文件都会存放在这个目录里。一个数据库下面通常三个文件

        - **db.opt**
          - 用来存储当前数据库的默认字符集和字符校验规则。
        - **t_order.frm**
          -   t_order 的表结构会保存在这个文件 在 MySQL 中建立一张表都会生成一个.frm 文件 该文件是用来保存每个表的元数据信息的，主要包含表结构定义
        - **t_order.ibd**  
          - t_order 的表数据会保存在这个文件

3. 表空间文件的结构是怎么样的？

   1. 表空间由段（segment）、区（extent）、页（page）、行（row）组成，InnoDB存储引擎的逻辑存储结构如图：

      > ![image-20250909161703090](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250909-161705-image-20250909161703090.png)

##### mysql 的null值是如何存放的

- MySQL 的 Compact 行格式中会用「NULL值列表」来标记值为 NULL 的列，NULL 值并不会存储在行格式中的真实数据部分。
- NULL值列表会占用 1 字节空间，当表中所有字段都定义成 NOT NULL，行格式中就不会有 NULL值列表，这样可节省 1 字节的空间。

##### MySQL 怎么知道 varchar(n) 实际占用数据的大小？

​	MySQL 的 Compact 行格式中会用「变长字段长度列表」存储变长字段实际占用的数据大小。

varchar(n) 中 n 最大取值为多少？ 65535，具体还要减去所有字段的长度 + 变长字段字节数列表所占用的字节数 + NULL值列表所占用的字节数 <= 65535。

mysql 行溢出后 ，如何处理的 [https://www.cnblogs.com/ZhuChangwu/p/14035330.html](https://www.cnblogs.com/ZhuChangwu/p/14035330.html)

##### mysql的分页优化

- https://mp.weixin.qq.com/s/i6FL1iRECiWZ1CCf_juxQQ

- 分页之所以慢是因为 当其实页不为第一页时候，先会查出来固定的数量的数据，然后进行丢弃，以及会有一些回表问题

- select * from page order by id limit 6000000, 10; 

  - server层会调用innodb的接口，由于这次的offset=6000000，会在innodb里的主键索引中获取到第0到（6000000 + 10）条完整行数据，返回给server层之后根据offset的值挨个抛弃，最后只留下最后面的size条，也就是10条数据，放到server层的结果集中，返回给客户端。

  - 优化select * from page  where id >=(select id from page  order by id limit 6000000, 1) order by id limit 10;

    - 数据量较大时候，有一定的提升

    - 不同的地方在于，在返回server层的过程中，只会拷贝数据行内的id这一列，而不会拷贝数据行的所有列，当数据量较大时，这部分的耗时还是比较明显的。

- 非主键索引

  - select * from page order by user_name  limit 0, 10;

  - select * from page t1, (select id from page order by user_name limit 6000000, 100) t2  WHERE t1.id = t2.id;

- 深度分页问题

  - 如果是取出mysql表所有的数据，可以记录一下每次分页的最大ID，然后依次遍历 取固定数量

  - 如果是给用户做分页展示

    - battle一下 需求不合理

      - 什么样的翻页，需要翻到10多万以后，这明显是不合理的需求。

      - 一般来说，谷歌搜索基本上都在20页以内，作为一个用户，我就很少会翻到第10页之后。

      - 或者设计只有上一页下一页这种，就可以使用start_id为起始位。查询一直稳定。类似抖音一样，只能上划下滑，美其名曰瀑布流...



##### mysql单表要不要超过2000千万

- [https://xiaolincoding.com/mysql/index/2000w.html#%E5%AE%9E%E9%AA%8C](https://xiaolincoding.com/mysql/index/2000w.html#%E5%AE%9E%E9%AA%8C)

##### mysql为啥用b+树做索引

- [https://xiaolincoding.com/mysql/index/2000w.html#%E5%AE%9E%E9%AA%8C](https://xiaolincoding.com/mysql/index/2000w.html#%E5%AE%9E%E9%AA%8C)

##### mysql--索引--count(*)和count(1)有什么区别，那种性能最好

- [https://www.xiaolincoding.com/mysql/index/count.html#%E5%93%AA%E7%A7%8D-count-%E6%80%A7%E8%83%BD%E6%9C%80%E5%A5%BD](https://www.xiaolincoding.com/mysql/index/count.html#哪种-count-性能最好)

- 结论

  - count(*)=count(1)>count(主键字段)>count(字段)

- count()

  - 统计符合查询条件的记录中，函数指定的参数不为null的记录有多少个

  - count计数的时候如果有二级索引 ，会优先走二级索引，因为二级索引不包含实际数据，所以二级索引树更小，遍历的IO成本小

  - count(1)的效率更高，因为不需要判断具体的列

- count(*) 执行过程是怎样的？

  - ，也就是说，当你使用 count(*)  时，MySQL 会将 参数转化为参数 0 来处理。
    - InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. There is no performance difference 官网

- 小结

  - count(1)、 count(*)、 count(主键字段)在执行的时候，如果表里存在二级索引，优化器就会选择二级索引进行扫描。

  - 所以，如果要执行 count(1)、 count(*)、 count(主键字段) 时，尽量在数据表上建立二级索引，这样优化器会自动采用 key_len 最小的二级索引进行扫描，相比于扫描主键索引效率会高一些。

  - 再来，就是不要使用 count(字段)  来统计记录个数，因为它的效率是最差的，会采用全表扫描的方式来统计。如果你非要统计表中该字段不为 NULL 的记录个数，建议给这个字段建立一个二级索引。

- 如何优化 count(*)？

  - 如果对一张大表经常用 count(*) 来做统计，其实是很不好的。一千多万数据，可能要5秒左右

    - 第一种，近似值 explain 命令 ，返回的rows就是近似值，速度快很多

    - 第二种，额外表保存计数值

##### mysql优化部分

> - 为啥要优化
>
>   - 系统的吞吐量瓶颈往往出现在数据库的访问速度上
>
>   - 随着应用程序的运行，数据库的中的数据会越来越多，处理时间会相应变慢
>
>   - 数据是存放在磁盘上的，读写速度无法和内存相比
>
> - 如何优化
>
>   - 设计数据库时：数据库表、字段的设计，存储引擎
>
>   - 利用好MySQL自身提供的功能，如索引等
>
>   - 横向扩展：MySQL集群、负载均衡、读写分离
>
>   - SQL语句的优化（收效甚微）
>
> - 字段设计
>
>   - 原则：尽量使用整型表示字符串 （个人觉得一些状态用数值比较好，比如存IP，虽然可以节省约四倍空间，但是没必要）
>
>     - 存储IP 
>
>     - INET_ATON(str)，address to number
>
>     - INET_NTOA(number)，number to address
>
>   - MySQL内部的枚举类型（单选）和集合（多选）类型
>     - 但是因为维护成本较高因此不常使用，使用关联表的方式来替代enum	
>
>   - 定长和非定长数据类型的选择
>     - decimal不会损失精度，存储空间会随数据的增大而增大。double占用固定空间，较大数的存储会损失精度。非定长的还有varchar、tex
>
>   - 金额
>
>     - 对数据的精度要求较高，小数的运算和存储存在精度问题（不能将所有小数转换成二进制）
>
>     - 定点数decimal
>
>       - price decimal(8,2)有2位小数的定点数，定点数支持很大的数（甚至是超过int,bigint存储范围的数）
>
>       - oracle 数值 存储 https://www.cnblogs.com/kerrycode/p/3265120.html
>
>     - 小单位大数额避免出现小数
>
>   - 字符串存
>     - 定长char，非定长varchar、text（上限65535，其中varchar还会消耗1-3字节记录长度，而text使用额外空间记录长度）
>
>   - 原则：字段注释要完整，见名知意

##### IP存整形的好处

> - 在真实开发中，即使公司不在意空间节省，将 IP 地址存储为整型数值（通过 INET_ATON 转换）仍然非常有必要。空间优化只是最直观的好处，其核心价值在于性能提升、查询效率、数据一致性和可维护性。以下是关键原因：
>
> - 一、核心优势（远超空间节省）
>
>   - \1. 极致的查询性能优化：
>
>     - 等值查询快 10-100 倍
>
>     - 整型比较是 CPU 原生指令（单周期完成），字符串比较需逐字符匹配（涉及字符集、大小写等复杂逻辑）。
>
>     - 范围查询（如 IP 段）快 1000 倍以上
>
>       - ✅ 整型方案：
>
>       - ``{{CODE-START}}sql        WHERE ipint BETWEEN INETATON('192.168.1.1') AND INET_ATON('192.168.1.255')        {{CODE-END}}`{{CODE-START}}        → 直接走索引，毫秒级响应
>
>       - ❌ 字符串方案：
>
>       - {{CODE-END}}`{{CODE-START}}sql        WHERE ip_str LIKE '192.168.1.%'   -- 无法用索引，全表扫描        -- 或更复杂的正则表达式        {{CODE-END}}`{{CODE-START}}        → 百倍以上性能差距，大数据量直接超时
>
>     - 场景示例：查 192.168.1.1 ~ 192.168.1.255 的访问记录
>
>   - \2. 索引效率质的飞跃：
>
>     - 索引大小降低 3-4 倍
>
>     - {{CODE-END}}INT UNSIGNED（4字节） vs VARCHAR(15){{CODE-START}}（16字节+变长开销）
>
>     - B+树层级更浅：
>
>     - 相同数据量下，整型索引的 B+树高度更低，减少磁盘 I/O（性能核心瓶颈）。
>
>     - 内存利用率提升：
>
>     - 更多索引页可缓存到 InnoDB Buffer Pool，降低慢查询概率。
>
>   - \3. 子网/CIDR 查询的高效实现：
>
>     - 业务场景：安全审计、地域分析、反作弊系统需频繁查 IP 段。
>
>     - 整型方案：通过简单的 {{CODE-END}}BETWEEN 或位运算（如 ip & netmask = subnet{{CODE-START}}）高效完成。
>
>     - 字符串方案：需拆分子网起止 IP 或依赖低效函数，几乎无法优化。
>
> - 二、数据一致性与维护性
>
>   - \1. 杜绝格式问题：
>
>     - 字符串存储易出现 {{CODE-END}}'192.168.01.001'（前导零）、' 192.168.1.1'{{CODE-START}}（空格）、大小写不一致等问题。
>
>     - 整型存储通过 {{CODE-END}}INET_ATON{{CODE-START}} 自动标准化，写入即保证合法性。
>
>   - \2. 外键关联更高效：
>     - 当 IP 需要关联到地理信息表时，整型外键比字符串外键的 JOIN 速度快 5-10 倍。
>
>   - \3. 未来扩展性：
>     - 使用整型存储天然支持 IPv6 的扩展方案（{{CODE-END}}VARBINARY(16){{CODE-START}}），架构统一。
>
> - 三、性能对比实测数据
>
>   - | 场景                | 字符串存储 (VARCHAR) | 整型存储 (INT UNSIGNED) | 性能差距       |
>     | ------------------- | -------------------- | ----------------------- | -------------- |
>     | 等值查询 (1000万行) | 120 ms               | 0.5 ms                  | **快 240 倍**  |
>     | IP 段查询 (BETWEEN) | 4200 ms (全表扫描)   | 2 ms (索引覆盖)         | **快 2100 倍** |
>     | 索引大小            | 220 MB               | 70 MB                   | **缩小 70%**   |
>
>   - \> 注：基于 MySQL 8.0 + InnoDB 的测试环境
>
> - 四、何时可不转换？
>
>   - 仅在以下场景考虑直接存字符串：
>
>   - \1. 极低频访问的表（如配置表 < 1000 行）
>
>   - \2. 纯展示用途（从不作为查询条件）
>
>   - \3. 临时日志表（保留周期 < 24 小时）
>
> - 结论：必须用整型存储
>
>   - \1. 核心价值：
>
>   - ⚡️ 查询性能提升百倍级（尤其范围查询）    📦 索引效率质变（降低 I/O 压力）    🔒 数据强一致性（避免脏数据）
>
>   - \2. 实施成本几乎为零：
>
>     - 写入时 {{CODE-END}}INETATON()，查询时 INETNTOA()`
>
>     - 应用层可通过工具类/ORM 字段自动封装，业务无感知
>
>     - 即使公司不关心存储成本，性能问题和慢查询引发的系统瘫痪、用户体验下降、运维成本飙升才是真正的业务痛点。IP 存整型是数据库优化的经典实践，拒绝此方案相当于主动放弃唾手可得的性能红利。