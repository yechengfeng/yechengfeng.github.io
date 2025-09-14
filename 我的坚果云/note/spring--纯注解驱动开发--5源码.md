

# 

- 参考
  - [Spring BeanDefinition加载流程分析](https://www.cnblogs.com/coder-zyc/p/14583011.html)

### spring 容器的refresh() 创建刷新

- #### 1 prepareRefresh() 刷新前的预处理工作

  1. initPropertySources() 

     > AbstractApplicationContext中的实现是空的，初始化一些属性设置，子类自定义个性化的属性方法

  2. getEnvironment().validateRequiredProperties();

     > 检验属性的合法等

  3. this.earlyApplicationListeners = new LinkedHashSet<>(this.applicationListeners);

     > 保存容器中的一些早期的事件，

- #### 2 ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory(); //获取beanfactory

  1. refreshBeanFactory ;

     - 创建了一个 this.beanFactory =new DefaultListableBeanFactory();

       ```java
       public GenericApplicationContext() {
       		this.beanFactory = new DefaultListableBeanFactory(); 无参构造
       	}
       
       ```

     - 设置ID

  2. getBeanFacotry

     - 返回刚刚创建的beanFactory

- #### 3 prepareBeanFactory(beanFactory); //beanFactory的预准备工作(BeanFactory进行一些设置)

  1. 设置beanFactory的类加载器，支持表达式解析器

  2. 添加部分BeanPostProcessor(ApplcationContextAwareProcessor)
  3. 设置忽略的自动装配的接口EnvironmentAware,EmbeddedValueResolverAware, 
  4. 注册可以解析的自动装配，我们可以在任何组件中自动装配，BeanFactory,ResourceLoader,ApplicationEventPublisher
  5. 添加BeanPostProcessor(ApplicationListenerDetector）
  6. 添加编译时候的AspectJ;
  7. 到给BeanFactory中注册一些能用的组件
     - Environment（ConfigureableEnvironment）
     - systemProperties  Map<String,Object>
     - systemEnviroment  Map<String,Object>

- #### 4 postProcessBeanFactory()    

  >  beanFactory准备工作完成后进行的后置处理工作

  - 子类通过重写这个方法赖在beanFactory创建并且与准备完成之后做进一步的设置

-----------------------上面是BeanFactory的创建以及预准备工作----------------------------------------------------------------------

- #### 5.invokeBeanFactoryPostProcessors(beanFactory); 

  > 执行beanFactoryProcessor
  >
  > BeanFactoryPostProcessor : BeanFactory的后置处理器。在beanFactory标准初始化之后执行;
  >
  > 俩个接口:**BeanFactoryPostProcessor**     **BeanDefinitionRegistryPostProcessor**

  1. 执行BeanFactoryPostProcessor的方法
     1. 先执行BeanDefinitionRegistryPostProcessor
        - 获取所有的BeanDefinitionRegistryPostProcessor
        - 先执行实现了PriorityOrded优先级接口的BeanDefinitionRegistryPostProcessor，`postProcessor.postProcessBeanDefinitionRegistry(registry)`
        - 再执行实现了Ordered顺序接口的BeanDefinitionRegistryPostProcessor `postProcessor.postProcessBeanDefinitionRegistry(registry)`
        - 最后执行没有实现任何优先级的BeanDefinitionRegistryPostProcessor
     2. 再执行BeanFactoryPostProcessor类型
        - 获取所有的BeanFactoryPostProcessor
        - 先执行实现了PriorityOrded优先级接口的BeanFactoryPostProcessor，`postProcessor.postProcessBeanFactory(beanFactory)`
        - 再执行实现了Ordered顺序接口的BeanDefinitionRegistryPostProcessor `postProcessor.postProcessBeanFactory(beanFactory)`
        - 最后执行没有实现任何优先级的BeanDefinitionRegistryPostProcessor

- #### 6. registerBeanPostProcessors(beanFactory);

  > 注册bean的后置处理器 
  >
  > - BeanPostProcessor的子类接口 (不同接口类型的接口，在bean创建前后时机不一样)
  >
  >   - DestructionAwareBeanPostProcessor
  >
  >   - [InstantiationAwareBeanPostProcessor](https://www.cnblogs.com/teach/p/12642349.html) 
  >     - [https://www.cnblogs.com/jock766/p/18785794](https://www.cnblogs.com/jock766/p/18785794)
  >   - SmartInstantiationAwareBeanPostProcessor
  >   - MergedBeanDefinitionPostProcessor [被放在internalPostProcessors Map中]

  - 获取所有的BeanPostProcessor ，后置处理器默认都可以通过PriorityOrdered. Ordered来指定优先级。

  - 先注册PriorityOrdered优先级接口的BeanPostProcessor；把每一个BeanPostProcessor添加到BeanFactory中beanFactory.addBeanPostProcessor(postProcessor):

  -  再注册Ordered接口的

  - 最后注册没有实现任何优先级接口的

  - 最后注册MergedBeanDefinitionPostProcessor

  - 注册一个ApplicationListenerDetector来检查bean是否是ApplicationListener;如果是

    ```java
    applicationContext.addApplicationListener((ApplicationListener<?>)bean);|	
    ```

- #### 7. initMessageSource();初始化MessageSource组件(做国际化功能;消息绑定，消息解析)

  - 获取BeanFactory
  - 看容器中是否有ID为MessageSource组件.
    - 如果有复制给MessageSource组件，如果没有自己创建一个DelegatingMessageSource;
      - 一般用在取出国际化配置文件中的某个key值，能按照区域信息

- #### 8. initApplicationEventMulticaster(）；初始化事件派发器

  1. 获取beanFactory
  2. 从BeanFactory中获职applicationEventMulticaster的ApplicationEventMulticaster；
  3. 如果上一步设有配置；创建一个SimpleApplicationEventMulticaster
  4. 将创建的ApplicationEventMulticaster添加到BeanFactory中，以后其他组件需要的话，直接注入就可以了

- #### 9. onRefresh() 留给子类实现

  - 子类重写这个方法，在容器刷新的时候可以自定义逻辑

- #### 10 .registerListeners(); 给容器中所有的项目里面的ApplicationListener注册进来

  - 从容器中拿到所有的ApplicationListener

  - 将每个监听器添加到事件派发器中；

    ```java
    getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName)
    ```

  - 派发之前步骤产生的步骤

- #### 11. finishBeanFactoryInitialization (beanFactory) ;  //初始化所有剩下的单实例bean;

  1. beanFactory. preInstantiateSingletons (); 

     - 1获取容器中的所有beanName，依次进行初始化和创建对象

     - 2获取bean的定义信息，RootBeanDefinition

     - 3Bean不是抽象的，是单例的，并且不是懒加载的

       - 判断是否是FactoryBean;是否是实现Factory接口的Bean;

       - 不是FactoryBean ，利用getBean(beanName)创建对象

         1. doGetBean 

         2. 先获取缓存中保存的单实例bean.如果可以获取到，说明这个bean之前被创建过(所有创建过的bean都会被保存)

            - 从 这个容器拿   `Object singletonObject = this.singletonObjects.get(beanName);`

         3. 缓存中拿不到，开始创建bean

         4. 标记当前bean已经被创建

         5. 获取bean的定义信息

         6. 获取当前bean依赖的其他的bean；如果有按照getBean()把依赖的bean先创建出来

         7. 启动单实例bean

            - 1 createBean (beanName, mbd, args);

            - 2 Object bean = resolveBeforeInstantiationbeanName, mbdToUse);

              > 给Bean 让BeanPostProcessor先拦截返回代理对象 InstantiationAwareBeanPostProcessor的代理逻辑，先触发postProcessBeforeInstantiation();
              >
              > 如果有返回值 ，再触发postProcessAfterInitialization();

            - 3 如果没有返回代理对象，继续走下面的逻辑

            - 4 Obiect beaninstance = docreateBean(beanName. mbdToUse. ares ):

              - 1 创建bean实例createBeanInstance (beanName, mbd, args);利用工厂方法或者对象的构造器创建实例bean

              - 2 调用applyMergedBeanDefinitionPostProcessors 的后置处理器

              - 3 populateBean (beanName, mbd, instanceWrapper); 为属性赋值

                - 1 赋值前，拿到InstantiationAwareBeanPostProcessor后置处理器

                  ```java
                  postProcessAfterInstantiation();
                  ```

                - 拿到InstantiationAwareBeanPostProcessor后置处理器：

                ```java
                postProcessPropertyValues ( ); //返回属性需要的值
                ```

                - `applyPropertyValues (beanName, mbd, bw, pvys);` 应用Bean属性的赋值

              - 4 initializeBean

                - 1 invokeAwareMethods (beanName， bean)；执行xxxAware接口的方法

                - 2 执行后置处理器初始化之前的方法 applyBeanPostProcessorsBeforeInitialization

                  ```java
                  BeanPostProcessor.postProcessBeforeInitialization();
                  ```

                - 3 执行初始化方法  `invokeInitMethods (beanName， wrappedBean， mbd)；`

                  1. 是否是InitializingBean接口的实现：执行接口规定的初始化；
                  2. 是否自定义初始化方法；

                - 4 【执行后置处理器初始化之后】applyBeanPostProcessorsAfterInitialization

                  ```java
                  BeanPostProcessor.postProcessAfterInitialization()
                  ```

                - 5 .注册bean的销毁方法

              - 5 将创建的Bean添加到缓存中singletonobjects；ioc容器就是这些Map；很多的Map里面保存了单实例Bean，环境信息。

            所有Bean都利用 getBean创建完成以后：检查所有的Bean是否是SmartInitializingsingleton接口的；如果是：就执行afterSingletonsInstantiated方法

         12. finishRefresh();完成BeanFactory的初始化创建工作，IOC容器创建完成
             - initLifecycleProcessor 初始化和生命周期有关的后置处理器，LifecycleProcessor可以在BeanFactory onFrefresh和onClose进行回调.默认从容器中拿，如果没有，创建一个默认的，加入容器中
             - getLifecycleProcessor).onRefresh();
             - publishEvent (new ContextRefreshedEvent (this)) 发布容器发布事
             - liveBeansView.registerApplicationContext(this)

         ## 总结

         1. Spring容器在启动的时候，先会保存所有注册进来的Bean的定义信息；

            - xml注册bean；<bean>
            - 注解注册Bean； @service、@component。 @Bean、xxx

         2. Spring容器会在合适的时机创建这些Bean

            - 用到这个bean的时候：利用getBean创建bean；创建好以后保存在容器中；

            - 統一创建剩下所有的bean的时候：finishBeanFactoryInitialization()：

         3. 后置处理器：BeanPostProcessor
            - 每一个bean创建完成，都会使用各种后置处理器进行处理；来增强bean的功能：
              - AutowiredAnnotationBeanPostProcessor：处理自动注入
              - AnnotationAwareAspectJAutoproxyCreator：来做AOP功能：
              - AsyncAnnotationBeanPostProcessor  增强的功能注解：

         4. 事件驱动模型；

            Applicationlistener：事件监听；

            ApplicationEventMulticaster :事件派发;

         > 小问题，LifecycleProcessor是在finishRefresh中被创建的，如果我在一个普通bean中注入这个，那么为什么会注入成功 ？
         >
         > 因为 springboot-autoconfigure包下面的 META-INF 有个类， 里面包含了LifecycleAutoConfiguration ，已经把bean的定义信息提前注入到了容器中。用到的时候回去拿然后自动创建

         

         











