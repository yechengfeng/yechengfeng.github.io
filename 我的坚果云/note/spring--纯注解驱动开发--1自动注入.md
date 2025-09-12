- 【尚硅谷Spring注解驱动教程(源码级讲解)-哔哩哔哩】 [https://b23.tv/bEvV8Kf](https://b23.tv/bEvV8Kf)

#### 自动注入

1. **Configuration给bean容器中注册组件**

2.  **ComponentCan自动扫描组件，**
   
   - 自定义TypeFilter定义扫描范围
   
3. **Scope注解设置bean的作用域 单例或者多例**

   - 单例在容器启动时候就会创建对象

   - 多例在第一次拿这个Bean的时候创建对象

4. **Lazy bean懒加载** 
   
   - 第一次使用Bean的时候创建对象，并初始化
   
5. **Conditional注解，按照条件加载bean，spring 底层大量使用的注解，配合Condition接口,扫描的时候判断bean满不满足条件。**

6. **给容器中注册组件**

   1. 包扫描+组件标注注解 （Controller,Service，Repository ，Component...）
   2. **@bean的方式**（导入第三方包的组件）
   3. **Import**	(快速给容器中导入组件 ，id默认是全类名)

      - ImportSelector  （用的比较多）
        - import可以导入一个importSelector的接口实现类，接口实现类提供需要导入的包的全类名。
   
      - ImportBeanDefinitionRegistrar
        - import可以导入一个ImportBeanDefinitionRegistrar的接口实现类，接口实现类提供bean的注册信息。
   
   4. 使用Spring提供的FactoryBean（工厂bean） (整合其他框架用的比较多) 
      - spring定义的一个工厂bean , 如果getBean("factoryBean")拿到的是工厂创建的对象的实例
      - 如果想要拿到工厂可以加上&   getBean("&factoryBean") 或者用 getBeanFactoryBean.class); 
        - 为啥是加&呢，借用C语言，&取的是地址值，
   
7. **bean的生命周期**

   1. bean注解中 init，destroy方法
   2. InitializingBean接口定义初始化  DisposableBean接口定义销毁逻辑 
   3. PostConstruct在bean创建完成，并且属性赋值完成 ；PreDestroy在Bean销毁时候
   4. BeanPostProcessor 接口 初始化前后进行调用
      1. postProcessBeforeInitialization
      2. postProcessAfterInitialization

   - 源码解析
     - 遍历得到所有的BeanPostProcessor，挨个执行 applyBeanPostProcessorsBeforeInitialization.一旦返回null，后面的不执行。

   - 执行顺序

     - PopulateBean  属性填充

     - initializeBean

       - applyBeanPostProcessorsBeforeInitialization

       - invokeInitMethods

       - applyBeanPostProcessAfterInitialization

   - **spring很多类都使用了BeanPostProcessor ，(还有很多，)**

     - **ApplicationContextAwareProcessor**，
       - 可以把IO容器set到Bean属性中，就是通过实现了 BeanPostProcessor

     - **BeanValidationPostProcessor**
       - 对参数进行校验

     - **InitDestroyAnnotationBeanPostProcessor**
       - PostConstruct ，PreDestroy 注解的方法利用这个类进行调用

     - **AutowiredAnnotationBeanPostProcessor**
       - 处理 AutoWired 注解的注入

8. **属性赋值**

   - @value

     - 基本数值

     - SPEL #{ }

     - ${}配置文件中的值，或者运行环境变量里面的值

   - @PropertySource 加载外部的配置文件

     - 加载完之后可以通过value获取

     - 也可以通过environment.getProperty()获取属性值

   - PropertySources加载多个 PropertySource  

9. **自动装配**

   - AutoWired 

     1. 默认优先按照类型去容器中找到对应的组件，application.getBean(BookDao.class);

     2. 如果找到相同多个类型的组件，再将属性名称作为ID去容器中查找。

     3. Qualifier可以指定具体的ID，而不是将属性名称作为ID去容器中查找

     4. 默认 如果找不到指定的类型，那么就会报错，可以 required 指定为false，

     5. Primay注解 ，让spring自动装配的时候默认使用的装配

     6. AutoWired: 可以加在 构造器 ，参数，方法属性上 

        - bean标注的方法创建对象的时候，参数是从容器中拿的。

        - AutoWired加载构造器上，如果只有一个有参构造器，这个autoWired可以省略

   - Resource注解（JSR250）
     - 默认按照属性名称自动装配的，没有支持primary的功能，也没有支持 required 指定为false的属性

   - Inject(没必要了解，好像已经淘汰了)

   - 自定义组件如果想要使用spring容器底层的一些组件（ApplicationContext, BeanFactory）

     - 只需要实现AWare接口

       - ApplicationContextAware

       - BeanNameAware 
         - 获取当前Bean的name

       - EmbeddedValueResolverAware 
         - 提供字符串解析的底层组件，可以把解析的字符串打印出来 

     - AWare是利用 后置处理器来实现的

10. .**环境切换**

    - Profile注解可以使仅在某一个环境生效 

    - 可以作用在类上，也可以在bean上