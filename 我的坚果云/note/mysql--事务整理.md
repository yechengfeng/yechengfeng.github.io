- [https://xiaolincoding.com/mysql/transaction/mvcc.html#](https://xiaolincoding.com/mysql/transaction/mvcc.html#)

- 事务有哪些特性？

  - 事务看起来感觉简单，但是要实现事务必须要遵守 4 个特性，分别如下：

    - 原子性（Atomicity）

    - 一致性（Consistency）

    - 隔离性（Isolation）

    - 持久性（Durability）：

  - InnoDB 引擎通过什么技术来保证事务的这四个特性的呢？

    - 持久性是通过redo log (重做日志）来保证的；

    - 原子性是通过 undo 10g(回滚日志） 来保证的；

    - 隔离性是通过 MVCC（多版本并发控制） 或锁机制来保证的；

    -  一致性则是通过持久性+原子性＋隔离性来保证；

- 并行事务会引发什么问题？

  - MySQL 服务端是允许多个客户端连接的，这意味着 MySQL 会出现同时处理多个事务的情况。

  - 在同时处理多个事务的时候，就可能出现脏读（dirty read）、不可重复读（non-repeatable read）、幻读（phantom read）的问题。

- 事务的隔离级别有哪些？

  - 读未提交(read uncommittea)，指一个事务还没提交时，它做的变更就能被其他事务看到；

  - 读提交 (read committed)，指一个事务提交之后，它做的变更才能被其他事务看到；

  - 可重复读 (repeatable read)，指一个事务执行过程中看到的数据，一直跟这个事务启动时看到的数据是一致的，MySQL InnoDB 引擎的默认隔离级别；

  - 串行化 (serializable)；会对记录加上读写锁，在多个事务对这条记录进行读写操作时，如果发生了读写冲突的时候，后访问的事务必须等前一个事务执行完成，才能继续执行；

  - MySQL InnoDB 引擎的默认隔离级别虽然是「可重复读」，但是它很大程度上避免幻读现象（并不是完全解决了，解决方案有俩种

    - 针对快照读（普通 select 语句），是通过 MVCC 方式解决了幻读，，因为可重复读隔离级别下，事务执行过程中看到的数据，一直跟这个事务启动时看到的数据是一致的，即使中途有其他事务插入了一条数据，是查询不出来这条数据的，所以就很好了避免幻读问题。

    - 针对当前读（select ... for update 等语句）是通过 next-key lock（记录锁+间隙锁）方式解决了幻读，因为当执行 select ... for update 语句的时候，会加上 next-key lock，如果有其他事务在 next-key lock 锁范围内插入了一条记录，那么这个插入语句就会被阻塞，无法成功插入，所以就很好了避免幻读问题。

- Read View 在 MVCC 里如何工作的？

- 可重复读是如何工作的？

- 读提交是如何工作的？

- MySQL 可重复读隔离级别，完全解决幻读了吗？

  - [https://www.xiaolincoding.com/mysql/transaction/phantom.html](https://www.xiaolincoding.com/mysql/transaction/phantom.html)