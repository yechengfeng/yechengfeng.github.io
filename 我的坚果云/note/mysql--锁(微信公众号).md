https://mp.weixin.qq.com/s/MhzRamB1nzJe9xA7HO-Wfg

## 锁粒度

- 锁的粒度指的是锁所作用的范围。MySQL 中锁的粒度从小到大主要有：行级锁、页级锁、表级锁。不同粒度的锁各有优缺点：

  - 粒度越小，并发度越高，但加锁、释放锁的开销越大，容易产生死锁。

  - 粒度越大，并发度越低，但加锁、释放锁的开销越小，死锁概率也低。

## 行级锁（Row-Level Locks）

- 行级锁是 MySQL 中粒度最小的锁，它只锁定数据表中的某一行或多行记录。InnoDB 存储引擎支持行级锁，这也是 InnoDB 成为高并发场景下首选存储引擎的重要原因之一。

  - 缺点：加锁、释放锁的开销较大，可能会产生死锁。

  - 优点：并发度高，多个事务可以同时操作表中不同行的数据，互不干扰。

##### 行级锁的种类

- **共享锁**（S 锁，Shared Locks）：又称读锁。事务对某一行加上 S 锁后，其他事务可以对该行加 S 锁，但不能加排他锁（X 锁）。只有当所有 S 锁都释放后，才能加 X 锁。
  - 事务 A 执行```SELECT * FROM user WHERE id = 1 FOR SHARE;```此时事务 A 对 id=1 的行加了 S 锁
  - 事务 B 可以执行```SELECT * FROM user WHERE id = 1 FOR SHARE```获取 S 锁进行读取
  - 但如果事务 B 执行```UPDATE user SET name = '张三' WHERE id = 1```;尝试加 X 锁，则会被阻塞，直到事务 A 提交或回滚释放 S 锁。

- **排他锁**（X 锁，Exclusive Locks）：又称写锁。事务对某一行加上 X 锁后，其他事务既不能对该行加 S 锁，也不能加 X 锁，只能等待该 X 锁释放。
  - 事务 A 执行SELECT * FROM user WHERE id = 1 FOR UPDATE;，对 id=1 的行加了 X 锁。此时事务 B 无论是执行SELECT * FROM user WHERE id = 1 FOR SHARE;还是UPDATE user SET name = '张三' WHERE id = 1;，都会被阻塞，直到事务 A 释放 X 锁。

- InnoDB 的行级锁是通过索引来实现的。如果查询语句没有使用索引，那么 InnoDB 会使用表级锁，锁住整个表。这是因为没有索引的话，数据库无法快速定位到具体的行，只能扫描全表，为了保证数据一致性，只能锁定整个表。

**表级锁**（Table-Level Locks）