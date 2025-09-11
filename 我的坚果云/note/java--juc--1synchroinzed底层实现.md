# Java synchronized 中的 wait() 和 notify() 实现原理

## 1. 基本概念
- **synchronized**
  - Java 提供的一种内置锁（Intrinsic Lock），基于对象监视器（Monitor）
  - 每个对象在 JVM 层都有一个与之关联的 Monitor
- **wait/notify/notifyAll**
  - 定义在 `Object` 类中（不是 Thread）
  - 必须在 synchronized 同步块或同步方法中调用，否则抛出 `IllegalMonitorStateException`

---

## 2. wait() 的实现机制
1. 线程持有某个对象的锁（即进入 synchronized 块/方法）
2. 调用 `obj.wait()` 后：
   - 线程会**释放当前对象的锁**（避免死锁）
   - 线程进入 **等待队列 (Wait Set)**，进入 **WAITING / TIMED_WAITING** 状态
   - 等待其他线程调用 `notify()` 或 `notifyAll()` 唤醒
3. 被唤醒后，线程需要重新尝试获取该对象的锁，才能继续往下执行

---

## 3. notify()/notifyAll() 的实现机制
1. 调用 `obj.notify()`：
   - 会在 **等待队列 (Wait Set)** 中随机唤醒一个线程（不保证公平性）
   - 被唤醒的线程不会立即执行，而是先进入 **锁竞争队列 (Entry List)**，重新尝试获取锁
   - 获取到锁后才能继续执行
2. 调用 `obj.notifyAll()`：
   - 会唤醒所有在等待队列中的线程
   - 这些线程同样要进入锁竞争队列，最终由调度器决定谁能获取锁

---

## 4. 底层实现（JVM 层）
- **Monitor 对象**
  - 每个 Java 对象在 JVM 中都有一个 Monitor
  - Monitor 内部维护两个主要队列：
    1. **Entry List**：所有尝试进入 synchronized 的线程在这里排队
    2. **Wait Set**：已经持有锁，但执行 `wait()` 后挂起的线程在这里等待
- **字节码**
  - `synchronized` 编译后会生成：
    - `monitorenter`（进入对象监视器，尝试加锁）
    - `monitorexit`（退出对象监视器，释放锁）
- **native 方法**
  - `wait()` / `notify()` / `notifyAll()` 底层由 JVM 的 **native 方法** 实现
  - 在 HotSpot JVM 中，它们最终调用的是操作系统的线程调度 API（如 Linux 的 futex）

---

## 5. 线程状态切换
- **wait()**
  - 从 **RUNNABLE** → **WAITING/TIMED_WAITING**
  - 同时释放锁
- **notify()/notifyAll()**
  - 线程从 **WAITING** → **BLOCKED**
  - 需要重新竞争锁，成功后才回到 **RUNNABLE**

---

## 6. 示例代码

```java
class WaitNotifyDemo {
    private final Object lock = new Object();
    private boolean condition = false;

    public void waitingMethod() throws InterruptedException {
        synchronized (lock) {
            while (!condition) {
                System.out.println(Thread.currentThread().getName() + " waiting...");
                lock.wait(); // 进入 Wait Set，释放锁
            }
            System.out.println(Thread.currentThread().getName() + " resumed!");
        }
    }

    public void notifyingMethod() {
        synchronized (lock) {
            condition = true;
            lock.notify(); // 随机唤醒一个等待的线程
            // lock.notifyAll(); // 唤醒所有等待线程
        }
    }
}

```

## 7. C++实现

```C++
void ObjectMonitor::wait(jlong millis) {
    // 1. 当前线程释放锁
    exitMonitor();

    // 2. 进入 WaitSet
    addToWaitSet(Self);

    // 3. 阻塞当前线程（操作系统层 futex/park）
    park();

    // 4. 被 notify 唤醒后，重新竞争锁
    enterMonitor();
}

void ObjectMonitor::notify() {
    // 从 WaitSet 中随机选一个线程
    ObjectWaiter* waiter = _WaitSet.dequeue();
    if (waiter != NULL) {
        // 放到 EntryList，让它重新去竞争锁
        _EntryList.enqueue(waiter);
    }
}

```

