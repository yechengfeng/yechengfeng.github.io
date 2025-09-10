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