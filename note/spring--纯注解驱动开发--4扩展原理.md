- ### BeanFactoryPostProcessor
  
  - **BeanPostProcessor**
  
    - bean的后置处理器,
  
  - **BeanFactoryPostProcessor**
  
    - beanFactory的后置处理器，在beanFactory标准初始化之后调用，所有的bean定义已经保存加载到beanFactory中，但是bean的实例还没有创建
  
    > 1. IOC容器创建对象
    > 2. invokeBeanFactoryFactoryPost
  
- ### BeanDefinitionRegistryPostProcessor 
  
  ```java
  //在所有的bean定义信息要被加载，bean实例还未创建的 
  interface BeanDefinitionRegistryPostProcessor extends BeanFactoryPostProcessor{
    	void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException;
  }
  ```
  
- ### ApplicationListener
  
  > 监听容器中发布的事件，事件驱动模型
  >
  > ```java
  > public interface  ApplicationListener  <E extends ApplicationEvent {
  > @override
  > public ovid onApplicationEvent(Application event){
  > 
  > }
  > }
  > 
  > //ContextRefreshEvent:容器刷新完成，会发布这个事件
  > //ContextCloseEvent 关闭容器会发布这个事件
  > 
  > ```
  >
  > 原理 
  >
  > ContextRefreshEvent
  >
  > 	1. 容器创建对象，refresh();
  > 	2. finishRefresh(); 容器刷新完成
  > 	3. publishEvent(new ContextRefreshEvent(this));
  > 	 - 获取事件的多播器 getApplicationEventMulticaster()
  > 	 - multicastEvent派发事件
  > 	 - 获取到所有的ApplicationListenner
  > 	   - 如果有Executor，可以支持异步，invokeListener(Listener ,event)
  > 	 
  > 	 事件多播器
  > 	  	refresh()
  > 	  		- initApplicationEventMulticaster //初始化
  > 	  		
  > 	  		
  > 	 SmartInitializingSingleton原理
  > 	 	- ioc容器创建对象并refresh的时候
  > 	 	- finishBeanFactoryInitialization(beanfactory);初始化剩下的单实例bean；
  > 	 		- 先创建所有的单实例bean;
  > 	 		- 获取所有创建好的单实例bean, 判断是否是smartInitializingSinton类型的
  > 	 		- 如果是就调用aftersingletonsInstantiated
  > 	  		
  > 	  		

### 解析原理

> ---
>
> 核心源码位于 `spring-context` 模块的 `org.springframework.context.event` 包中。
>
> #### 1. 事件发布：`publishEvent`
>
> 入口在 `ApplicationEventPublisher` 的实现类 `AbstractApplicationContext` 中：
>
> ```java
> @Override
> public void publishEvent(ApplicationEvent event) {
>     publishEvent(event, null);
> }
> 
> protected void publishEvent(Object event, @Nullable ResolvableType eventType) {
>     // ... 一些包装和类型处理代码
> 
>     // 获取应用程序事件多路广播器
>     ApplicationEventMulticaster multicaster = getApplicationEventMulticaster();
>     // 委托给多路广播器来发布事件
>     multicaster.multicastEvent(applicationEvent, eventType);
> }
> ```
>
> #### 2. 事件广播：`SimpleApplicationEventMulticaster`
>
> 默认的广播器实现是 `SimpleApplicationEventMulticaster`。
>
> ```java
> @Override
> public void multicastEvent(final ApplicationEvent event, @Nullable ResolvableType eventType) {
>     ResolvableType type = (eventType != null ? eventType : resolveDefaultEventType(event));
>     // 1. 根据事件类型，获取所有匹配的监听器
>     for (final ApplicationListener<?> listener : getApplicationListeners(event, type)) {
>         // 2. 判断是否有配置任务执行器（TaskExecutor）
>         Executor executor = getTaskExecutor();
>         if (executor != null) {
>             // 3. 如果配置了执行器，则异步执行监听器
>             executor.execute(() -> invokeListener(listener, event));
>         } else {
>             // 4. 默认情况：同步执行监听器
>             invokeListener(listener, event);
>         }
>     }
> }
> ```
>
> *   `getApplicationListeners(event, type)`：这个方法会从所有注册的监听器中，筛选出对该事件类型感兴趣的监听器。它维护了一个监听器的缓存， key 是事件类型， value 是该类型对应的监听器列表，以提高检索效率。
>
> #### 3. 监听器的注册
>
> 监听器是如何被容器知道的？
>
> *   **对于实现 `ApplicationListener` 接口的Bean**：在Bean创建完成后，`ApplicationContext` 会检查它是否是 `ApplicationListener`，如果是，则调用 `addApplicationListener` 方法将其注册到 `ApplicationEventMulticaster` 中。
>     *   源码在 `AbstractApplicationContext#registerListeners()` 和 `AbstractApplicationContext#finishBeanFactoryInitialization()` 中。
>
> *   **对于 `@EventListener` 注解的方法**：这是通过一个后处理器 `EventListenerMethodProcessor` 来实现的。
>     *   在容器刷新时，`EventListenerMethodProcessor` 会扫描所有Bean。
>     *   对于每个Bean，查找其所有带有 `@EventListener` 注解的方法。
>     *   为每个找到的方法，动态创建一个 `ApplicationListener` 适配器（`ApplicationListenerMethodAdapter`），并将这个适配器注册到容器中。
>     *   当事件发布时，实际调用的是这个适配器的 `onApplicationEvent` 方法，它内部会再去调用你写的那个被注解的方法。
>
> #### 4. 源码总结
>
> 1.  **发布入口**：`AbstractApplicationContext.publishEvent()`。
> 2.  **广播核心**：`ApplicationEventMulticaster.multicastEvent()`。
> 3.  **执行策略**：`SimpleApplicationEventMulticaster` 根据是否有 `TaskExecutor` 决定同步/异步执行。
> 4.  **监听器获取**：通过 `getApplicationListeners()` 从注册表中筛选匹配的监听器。
> 5.  **监听器注册**：
>     *   接口方式：由容器自动检测并注册。
>     *   注解方式：由 `EventListenerMethodProcessor` 后处理器解析并生成适配器后注册。
>
