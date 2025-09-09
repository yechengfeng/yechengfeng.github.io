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

​	

