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
  5. 添加BeanPostProcessor(ApplicationListnerDetector
  6. 添加编译时候的AspectJ;
  7. 给BeanFactory中注册一些能用的组件
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
     1. 先执行BeanDefinitionRegistryPostProcessor型
        - 获取所有的BeanDefinitionRegistryPostProcessor
        - 先执行实现了PriorityOrded优先级接口的BeanDefinitionRegistryPostProcessor，`postProcessor.postProcessBeanDefinitionRegistry(registry)`
        - 再执行实现了Ordered顺序接口的BeanDefinitionRegistryPostProcessor `postProcessor.postProcessBeanDefinitionRegistry(registry)`
        - 最后执行没有实现任何优先级的BeanDefinitionRegistryPostProcessor
     2. 再执行BeanFactoryPostProcessor类型
        - 获取所有的BeanFactoryPostProcessor
        - 先执行实现了PriorityOrded优先级接口的BeanFactoryPostProcessor，`postProcessor.postProcessBeanFactory(beanFactory)`
        - 再执行实现了Ordered顺序接口的BeanDefinitionRegistryPostProcessor `postProcessor.postProcessBeanFactory(beanFactory)`
        - 最后执行没有实现任何优先级的BeanDefinitionRegistryPostProcessor

- 6. #### registerBeanPostProcessors(beanFactory);

     > 注册bean的后置处理器 
     >
     > - BeanPostProcessor的子类接口 (不同接口类型的接口，在bean创建前后时机不一样)
     >
     >   - DestructionAwareBeanPostProcessor
     >
     >   - InstantiationAwareBeanPostProcessor
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

- 7. #### initMessageSource();初始化MessageSource组件(做国际化功能;消息绑定，消息解析)

     - 获取BeanFactory
     - 看容器中是否有ID为MessageSource组件.
       - 如果有复制给MessageSource组件，如果没有自己创建一个DelegatingMessageSource;
         - 一般用在取出国际化配置文件中的某个key值，能按照区域信息

  

  





