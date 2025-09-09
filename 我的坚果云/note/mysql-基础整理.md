- ### MySQL常用函数

  - ### 文本处理函数

  - ### 文本处理函数

  - ###  数值处理函数

- ### 为什么使用视图

  - ##### 视图的创建

  - 视图的更新

- 为什么使用储存过程？

  -  执行存储过程

  - 使用参数的存储过程

  - 带有控制语句的存储过程

- 触发器

  - 创建触发器

  - INSERT触发器 

  - DELETE触发器

  - UPDATE触发器

- 索引分类

  - 从物理存储角度

  - 从逻辑角度

- 索引的特殊应用

  - AUTO_INCREMENT
    - innodb_autoinc_lock_mode 配置 ，分别等于1，,2,3时候效果
  - 索引覆盖
  - 使用索引进行排序

- 冗余和重复索引

- 索引重用

  - 避免多个范围条件

- 最佳左前缀法则

- #### 索引失效https://blog.csdn.net/qq_32331073/article/details/79041232

  - 在索引上使用表达式

  - 全值匹配 *

  - 在索引上使用表达式

  - range 类型查询字段后面的索引无效

  - 尽量使用覆盖索引

  - 使用不等于时索引失效

  - is (not) null 时索引失效

  - like 以通配符开头会导致全表扫描

  - varchar 类型不加单引号索引失效

  - 使用or时索引失效

- #### MySQL查询分析工具

  - **慢查询日志**

    - MySQL的慢查询日志是MySQL提供的一种日志记录，它用来记录在MySQL中响应时间超过阈值的语句，具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。long_query_time的默认值为10，意思是运行10S以上的语句，默认情况下，Mysql数据库并不启动慢查询日志，需要我们手动来设置这个参数，当然，如果不是调优需要的话，一般不建议启动该参数，因为开启慢查询日志会或多或少带来一定的性能影响。慢查询日志支持将日志记录写入文件，也支持将日志记录写入数据库表

    - slow_query_log 

  - **explain**

    - type（重要)
      - system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL ，

    - possible_keys

    - extra

      - Using fileSort  如果排序没有用到索引，会是这个，速度慢

      - 临时表Using temporary  数据量大，效率低

  - 慢查询基础：优化数据访问

    - 是否向数据库请求了不需要的数据

    - MySQL是否在扫描额外的记录

    - 扫描的行数和返回的行数

    - 扫描的行数和访问类型

  - 优化特定类型的查询

    - JOIN 优化
      - 小表驱动大表

    - order by优化
      - 尽量使用index方式排序，遵照索引的最佳左前缀

    - 优化策略

      - 1 Order by时select * 是一个大忌

      - \2. 尝试提高 sortbuffersize

      -  尝试提高 max_length_for_sort_data

  - 主从复制

    - 复制原理

      - 基于语句的复制

      - 基于行的复制

  - 设计备份方案

    - 在线备份还是离线备份

    - 逻辑备份还是物理备份

  - MySQL底层实现

  - 事务日志 redo log

  - 锁与事务实现原理
    - 对于MySQL而言，事务机制更多是靠底层的存储引擎实现的，在服务器层面只有表锁。支持事务的InnoDB存储引擎实现了行锁、gap锁、next-key锁。

  - 事务隔离级别

  - MVCC

    - undo log（保证事务原子性->事务回滚)

    - rollback segment（为了提高并发度)

    - 不同隔离级别下read_view的生成规则

    - InnoDB MVCC 与 理想 MVCC的区别

  - InnoDB锁分类
    - record lock

  - binlog与redo log的区别

- innoDB和MyISAM的区别? (*)

  - https://www.jianshu.com/p/a957b18ba40d

- count(1)、count(*)与count(列名)的执行区别

  - https://blog.csdn.net/iFuMI/article/details/77920767

- 主键，唯一索引区别

  - 1）主键一定会创建一个唯一索引，但是有唯一索引的列不一定是主键；

  - 2）主键不允许为空值，唯一索引列允许空值；

  - 3）一个表只能有一个主键，但是可以有多个唯一索引；

  - 4）主键可以被其他表引用为外键，唯一索引列不可以；

  - 5）主键是一种约束，而唯一索引是一种索引，是表的冗余数据结构，两者有本质的差别

- 《数据库系统概念》

- 《MySQL必知必会》

- 《MySQL技术内幕 : InnoDB存储引擎》

​	

