##### 1. CountDownLatch

> ##### 构造方法只有一个
>
> ```java
> public CountDownLatch(int count)	;//count位初始计数值
> ```
>
> ##### 常用的方法
>
> ```java
> public void await() throws InterruptedException{}//常用方法;调用此方法线程会被挂起，它会等待直到count的值为0才继续执行
> 
> ```
>
> ```java
> public void await(long timeout ,TimeUnit unit) throws InterruptedException{} //和await类似，但是会等待一个固定的时间.	
> ```
>
> ```java
> public void countDown();//将count的值减1
> ```
>
> ##### 使用场景
>
> 用户购买一个商品下单成功后，我们会给用户发送各种消息提示用户『购买成功』，比如发送邮件、微信消息、短信等。所有的消息都发送成功后，我们在后台记录一条消息表示成功。
>
> 实现原理

##### 2. ThreadLocal

> - #### 原理
>
>   - 每个线程是一个Thread实例，其内部维护一个threadLocals的实例成员，其类型是ThreadLocal.ThreadLocalMap。
>   - 通过实例化ThreadLocal实例，我们可以对当前运行的线程设置一些线程私有的变量，通过调用ThreadLocal的set和get方法存取。
>   - ThreadLocal本身并不是一个容器，我们存取的value实际上存储在ThreadLocalMap中，ThreadLocal只是作为TheadLocalMap的key。
>   - 每个线程实例都对应一个TheadLocalMap实例，我们可以在同一个线程里实例化很多个ThreadLocal来存储很多种类型的值，这些ThreadLocal实例分别作为key，对应各自的value，最终存储在Entry table数组中。
>   - 当调用ThreadLocal的set/get进行赋值/取值操作时，首先获取当前线程的ThreadLocalMap实例，然后就像操作一个普通的map一样，进行put和get。
>
> - ##### 内存模型
>
>   ![image-20250910132543155](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250910-132545-image-20250910132543155.png)
>
>   - 图中左边是栈，右边是堆。线程的一些局部变量和引用使用的内存属于Stack（栈）区，而普通的对象是存储在Heap（堆）区。
>
>     - 线程运行时，我们定义的TheadLocal对象被初始化，存储在Heap，同时线程运行的栈区保存了指向该实例的引用，也就是图中的ThreadLocalRef。
>
>     - 当ThreadLocal的set/get被调用时，虚拟机会根据当前线程的引用也就是CurrentThreadRef找到其对应在堆区的实例，然后查看其对用的TheadLocalMap实例是否被创建，如果没有，则创建并初始化。
>
>     - Map实例化之后，也就拿到了该ThreadLocalMap的句柄，那么就可以将当前ThreadLocal对象作为key，进行存取操作。
>
>     - 图中的虚线，表示key对应ThreadLocal实例的引用是个弱引用。
>
> - ##### 关于内存泄漏的问题
>
>   - 如果key 使用使用强引用
>
>     - 引用ThreadLocal的对象被回收了，但是ThreadLocalMap还持有ThreadLocal的强引用，如果没有手动删除，ThreadLocal不会被回收，导致Entry内存泄漏。
>
>   - key 使用弱引用
>
>     - 引用ThreadLocal的对象被回收了，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。value在下一次ThreadLocalMap调用set、get、remove的时候会被清除。
>
>     - 比较两种情况，我们可以发现：由于ThreadLocalMap的生命周期跟Thread一样长，如果都没有手动删除对应key，都会导致内存泄漏，但是使用弱引用可以多一层保障：弱引用ThreadLocal被清理后key为null，对应的value在下一次ThreadLocalMap调用set、get、remove的时候可能会被清除。
>
>     - 因此，ThreadLocal内存泄漏的根源是：由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。
>
> - #### ThreadLocal最佳实践
>
>   - 使用场景
>
>     - 当需要存储线程私有变量的时候。
>
>     - 当需要实现线程安全的变量时。
>
>     - 当需要减少线程资源竞争的时候。
>
> - #### 注意
>
>   - **每次使用完ThreadLocal，建议调用它的remove()方法，清除数据。**
>
>   - **尽量用全局变量 ，用static修饰**

