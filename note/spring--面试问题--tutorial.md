1. #### spring-adminer

   - [https://www.51cto.com/article/699076.html](https://www.51cto.com/article/699076.html)

2. #### SpringBoot实现定时任务的4中方式

   - 使用Timer
   - 使用ScheduledExecutorService
   - 使用Spring Task
   - 整合Quartz

3. #### SpringBoot中如何集成SSL

4. #### springboot starter启动原理

   - SpringBoot默认扫描启动类所在的包下的主类和子类的所有组件，但并没有包括依赖包中的类。那么starter中的bean是如何被发现和加载的呢？
   - @SpringBootApplication这个复合组件中有个@EnableAutoConfiguration的注解，借助@Import的支持，能够收集和注册依赖包中相关bean的定义。通过一层一层翻源码发现，可以发现SpringFactoriesLoader.loadFactoryNames方法调用了loadSpringFactories，从所有jar包中读取META-INF/spring.factories文件信息。spring.factories中配置的是被@Configuration注解修饰的配置类。（SPI技术）这样启动之后starter中的bean也被注入到了容器中

5. #### 如何自定义springboot starter [https://www.cnblogs.com/hello-shf/p/10864977.html](https://www.cnblogs.com/hello-shf/p/10864977.html)

   - 新建一个项目，命名格式采用xxx-spring-boot-starter。spring规定非官方的starter以xxx-spring-boot-starter，官方的以spring-boot-starter-xxx命名。
   - 添加starter中需要的依赖
   - 添加必要的配置类，可以写一些通用的模板类，然后在resource/下建META-INF/spring.factories文件，把配置类配到里面
   - 执行mvn clean install把jar包打到本地maven仓库，然后就可以使用了

6. #### springboot 是如何启动的 

   [https://juejin.cn/post/6844903652201594887#heading-0](https://juejin.cn/post/6844903652201594887#heading-0)

   - springbootApplication 包含了SpringBootConfiguration , EnableAutoConfiguration ,ComponentScan

   - 任何标注了Configuration的类都是一个JavaConfig配置类

   - ComponentScan的功能其实就是自动扫描并加载符合条件的组件（比如@Component和@Repository等）或者bean定义，最终将这些bean定义加载到IoC容器中。我们可以通过basePackages等属性来细粒度的定制@ComponentScan自动扫描的范围，如果不指定，则默认Spring框架实现会从声明@ComponentScan所在类的package进行扫描。

   - EnableAutoConfiguration看英文意思就是自动配置，概括一下就是，借助@Import的帮助，将所有符合自动配置条件的bean定义加载到IoC容器。
     - 里面最关键的是@Import(EnableAutoConfigurationImportSelector.class)，借助EnableAutoConfigurationImportSelector，@EnableAutoConfiguration可以帮助SpringBoot应用将所有符合条件的@Configuration配置都加载到当前SpringBoot创建并使用的IoC容器。该配置模块的主要使用到了SpringFactoriesLoader。

7. #### ApplicationContext的子类

   - ClassPathXmlApplicationContext(相对路径加载bean)
   - FileSystemXmlApplicationContext(项目的绝对路径加载bean)
   - AnnotationConfigApplicationContext(基于注解的方式加载bean)
   - AnnotationConfigServletWebServerApplicationContext(需要一个servlet容器)

8. #### Spring容器中Bean的作用域

   - **singleton**：单例模式，在整个Spring IoC容器中，使用singleton定义的Bean将只有一个实例
   - **prototype**：原型模式，每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例
   - **request**：对于每次HTTP请求，使用request定义的Bean都将产生一个新实例，即每次HTTP请求将会产生不同的Bean实例。只有在Web应用中使用Spring时，该作用域才有效
   - **session**：对于每次HTTPSession，使用session定义的Bean产生一个新实例。同样只有在Web应用中使用Spring时，该作用域才有效
   - **globalsession**：每个全局的HTTPSession，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portletcontext的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效
   - 其中比较常用的是singleton和prototype两种作用域。对于singleton作用域的Bean，每次请求该Bean都将获得相同的实例。容器负责跟踪Bean实例的状态，负责维护Bean实例的生命周期行为；如果一个Bean被设置成prototype作用域，程序每次请求该id的Bean，Spring都会新建一个Bean实例，然后返回给程序。**在这种情况下，Spring容器仅仅使用new 关键字创建Bean实例，一旦创建成功，容器不在跟踪实例，也不会维护Bean实例的状态**。
   - 如果不指定Bean的作用域，Spring默认使用singleton作用域。Java在创建Java实例时，需要进行内存申请；销毁实例时，需要完成垃圾回收，这些工作都会导致系统开销的增加。因此，prototype作用域Bean的创建、销毁代价比较大。而singleton作用域的Bean实例一旦创建成功，可以重复使用。因此，除非必要，否则尽量避免将Bean被设置成prototype作用域。

9. #### spring，springboot,springcloud区别

   - Spring Cloud 是一系列框架的有序集合。它利用 Spring Boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot 的开发风格做到一键启动和部署。

10. #### 说说Spring中的@Component和@Bean有什么区别？（参考 [https://www.bilibili.com/video/BV1P44y1N7QG?p=10&vd_source=8db2bf2ffb3ab22965fccab48d4389ba](https://www.bilibili.com/video/BV1P44y1N7QG?p=10&vd_source=8db2bf2ffb3ab22965fccab48d4389ba)）

    - **作用对象不同**
       - @Component： 类级别注解。标记在类定义上。

       - @Bean： 方法级别注解。标记在配置类 (@Configuration) 中的方法上

    - **控制权与创建方式不同：**

       - @Component： 声明式/自动。Spring 通过类路径扫描 (@ComponentScan) 自动发现并实例化该类（通常用无参构造器），注册为 Bean。开发者不控制实例化过程。

       - @Bean： 编程式/显式。开发者在方法体内编写代码 (new, 工厂方法, 复杂逻辑) 来创建、配置并返回对象实例。Spring 调用该方法并将返回对象注册为 Bean。开发者完全控制实例化过程和初始配置。

    - **主要用途不同：**

       - @Component： 主要用于注册你自己编写的、属于应用程序业务逻辑的类 (如 Service, Repository, Controller, Util 等)。通常结合 @ComponentScan 使用。

       - @Bean： 主要用于：

         - 注册第三方库的类（你无法修改其源码添加 @Component）为 Bean (如 DataSource, RestTemplate)。

         - 注册需要复杂初始化逻辑或特殊配置的 Bean。

         - 注册多个同类型但配置不同的 Bean (通过不同方法名区分)。

         - 当你需要完全掌控 Bean 的创建过程时。

    - **配置方式不同：**

       - @Component： 依赖于类路径扫描 (@ComponentScan) 来发现和注册 Bean。

       - @Bean： 定义在 @Configuration 类中，是显式配置的一部分。

11. ##### beanFactory和applicationContext区别

     >  参考 
     >
     > 1. [https://my.oschina.net/yao00jun/blog/215642](https://my.oschina.net/yao00jun/blog/215642)
     > 2. [https://blog.csdn.net/qq_36748278/article/details/78264764](https://blog.csdn.net/qq_36748278/article/details/78264764)

     - 与Application Context关系

        - Application context 的父接口

        -  spring的核心容器，主要的ApplicationContext实现都组合了BeanFactory的功能

     - beanFactory功能

        - 主要是getBean
          - defaultListableBeanFactory是其默认的实现

     - applicationContext不同之处，Application提供了更多的能力

        - MessageSource 国际化的能力
        - ResourcePatternResolver 获取资源的能力
        - ApplicationEventPublisher 事件发布
        - EnvironmentCapable  获取环境信息的能力

12. ##### spring中的AOP	

     - JDK动态代理，接口，用Proxy.newProxyInstance生成代理对象，InvocationHandler
     - CGLIB，类，用enhancer生成代理对象，MethodInteceptor
     - 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP ;
     - 如果目标对象实现了接口，可以强制使用CGLIB实现AOP ;如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换;

13. **spring 如何解决循环依赖的 https://blog.csdn.net/a745233700/article/details/110914620**

14. Spring 中常见的后置处理器

     - **AutowiredAnnotationBeanPostProcessor** （Autowired ，value注解）
     - **CommonAnnotationBeanPostProcessor**（处理Resource,@PostConstruct @PreDestroy）
     - **ConfigurationPropertiesBindingPostProcessor**（解析ConfigurationProperties注解）
     - **ContextAnnotationAutowireCandidateResolver**（解析注解字符串的?）

15. spring bean的生命周期[https://www.cnblogs.com/vipstone/p/16659553.html](https://www.cnblogs.com/vipstone/p/16659553.html)

     1.实例化：为 Bean 分配内存空间；
     2.设置属性：将当前类依赖的 Bean 属性，进行注入和装配；
     3.初始化：

     1. 执行各种通知；
     2. 执行初始化的前置方法；
     3. 执行初始化方法；
     4. 执行初始化的后置方法。
        4.使用 Bean：在程序中使用 Bean 对象；
        5.销毁 Bean：将 Bean 对象进行销毁操作。

16. **spring中常用到几种连接池  分别有什么不一样**

     1. C3P0

     2. HikariCP

        1. 性能之王： 获取和释放连接的速度是已知连接池中最快的，在高并发、低延迟要求的场景下优势极其明显。资源占用（CPU、内存）也最低。

     3. druid

        1. 特点： 阿里巴巴开源的高性能、功能强大的数据库连接池和监控组件。国内应用非常广泛。

        2. 功能极其丰富：

           - 强大的监控： 内置详尽的统计信息（SQL执行次数、时间、并发、连接池状态等），自带可视化 Web 页面，方便诊断性能瓶颈和 SQL 问题。

           - SQL 防火墙： 防御 SQL 注入，拦截运行不符合预期的 SQL。

           - 加密： 支持数据库密码加密。

           - 扩展性： 通过 Filter 链机制方便扩展功能。

           - 内置常用功能： 如 WallFilter (防火墙)、StatFilter (统计)、LogFilter (日志) 等。

     4. DBCP ( Apache Commons DBCP)

17. ##### springcloud 面试题 [https://www.cnblogs.com/wgjava/p/18699827](https://www.cnblogs.com/wgjava/p/18699827)

18. #### spring 事务

     1. 事务的传播类型

        1. Spring 事务中定义了 7 种事务传播类型，分别是：REQUIRED、SUPPORTS、MANDATORY、REQUIRES_NEW、NOT_SUPPORTED、NEVER、NESTED。其中最常用的只有 3 种，即：REQUIRED、REQUIRES_NEW、NESTED。

        2. REQUIRED

           - 子事务加入父事务中，即使父方法捕捉了异常，也是会回滚

        3. REQUIRES_NEW

           - REQUIRES_NEW 也是常用的一个传播类型，该传播类型的特点是：无论当前方法是否存在事务，子方法都新建一个事务。此时父子方法的事务时独立的，它们都不会相互影响。但父方法需要注意子方法抛出的异常，避免因子方法抛出异常，而导致父方法回滚。 

           - 父方法事务回滚了，但子方法事务不一定回滚

        4. NESTED

           - 当前方法存在事务时，子方法加入在嵌套事务执行。当父方法事务回滚时，子方法事务也跟着回滚。当子方法事务发送回滚时，父事务是否回滚取决于是否捕捉了异常。如果捕捉了异常，那么就不回滚，否则回滚。

     2. 事务失效场景

        1. 若同一类中的其他没有 @Transactional 注解的方法内部调用有 @Transactional 注解的方法，有 @Transactional 注解的方法的事务会失效。
        2. @Transactional 注解只有作用到 public 方法上事务才生效。
        3. 被 @Transactional 注解的方法所在的类必须被 Spring 管理。
        4. 底层使用的数据库必须支持事务机制，否则不生效。
        5. Spring 事务执行过程中，如果抛出非 RuntimeException 和非 Error 错误的其他异常，那么是不会回滚的哦

19. Spring Cloud Config用于集中管理分布式系统中的配置信息。它可以将配置文件存储在一个集中的位置（如Git仓库、SVN等），服务可以从配置中心获取自己所需的配置信息。这样可以方便地对配置进行管理和修改，而不需要在每个服务中修改配置文件，并且可以实现配置的动态更新，无需重启服务即可使配置生效。

20. Ribbon是一个客户端负载均衡器，它可以在服务消费者调用服务时，根据一定的策略将请求分配到不同的服务提供者实例上，以实现负载均衡。例如，当服务消费者调用多个服务提供者实例时，Ribbon可以根据轮询、随机、权重等策略将请求分发到不同的实例。在使用时，通常会在服务消费者的RestTemplate上添加@LoadBalanced注解，这样RestTemplate就具有了负载均衡的能力。在调用服务时，只需要使用服务名称，Ribbon会自动从Eureka获取服务实例列表，并根据负载均衡策略选择一个实例进行请求。

21. Spring Cloud Gateway的主要功能是什么？如何配置它？
    - Spring Cloud Gateway是Spring Cloud中的微服务网关，它提供了路由转发、请求过滤、限流等功能。可以根据请求的路径、头部信息等将请求转发到不同的微服务，并可以在请求转发前后进行过滤处理。

22. spring如何解决循环依赖的

    > 非常好，这是一个关于Spring框架核心机制的经典面试题。Spring通过一个**三级缓存（three-level cache）** 的机制，巧妙地解决了单例（Singleton）作用域下的Setter/字段注入的循环依赖问题。
    >
    > 要理解这个解决方案，我们需要先明确**前提**和**边界**。
    >
    > ### 1. 什么是循环依赖？
    >
    > 循环依赖就是两个或多个Bean相互依赖，形成了一个闭环。
    > *   **Bean A → 依赖 → Bean B**
    > *   **Bean B → 依赖 → Bean A**
    >
    > 或者更长的循环：
    > *   **Bean A → Bean B → Bean C → Bean A**
    >
    > ---
    >
    > ### 2. Spring解决循环依赖的前提条件
    >
    > Spring**并非能解决所有类型**的循环依赖，它有很大的限制：
    >
    > 1.  **必须是单例Bean（Singleton）**：Spring的三级缓存是针对单例模式设计的。原型（Prototype）作用域的Bean存在循环依赖时，会直接抛出 `BeanCurrentlyInCreationException` 异常。
    > 2.  **不能是构造器注入（Constructor Injection）**：如果循环依赖是通过构造器注入完成的，Spring无法解决，会抛出 `BeanCurrentlyInCreationException`。这是因为Java在调用构造器之前，必须完成参数实例化。要实例化A需要先实例化B，而要实例化B又需要先实例化A，这成了一个死锁，无法进行下去。
    > 3.  **注入方式必须是Setter注入或字段注入（@Autowired）**：这两种方式允许Spring先调用默认构造器（或无参构造器）**实例化**对象，然后再进行**依赖注入**。这个“实例化”和“注入”分离的过程，是解决循环依赖的关键。
    >
    > **结论：Spring只能解决单例模式下，通过Setter方法或字段注入（@Autowired）形成的循环依赖。**
    >
    > ---
    >
    > ### 3. 核心机制：三级缓存
    >
    > Spring容器内部维护了三个Map，也就是我们常说的“三级缓存”：
    >
    > ```java
    > // 一级缓存（成熟池）：存放已经完全初始化好的、成熟的单例Bean。
    > // 从这里取出的Bean可以直接使用。
    > private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
    > 
    > // 二级缓存（早期曝光池）：存放早期曝光的、原始的对象（已经实例化，但还未填充属性）。
    > // 用于解决循环依赖。
    > private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
    > 
    > // 三级缓存（对象工厂池）：存放ObjectFactory对象工厂。
    > // 这是解决循环依赖和AOP代理的关键。
    > private final Map<String, ObjectFactory<?>> singletonFactories = new ConcurrentHashMap<>(16);
    > ```
    >
    > #### 解决循环依赖的流程（以A和B相互依赖为例）
    >
    > 假设创建Bean A时，发现它依赖Bean B。
    >
    > **第一步：开始创建A**
    > 1.  `getBean(A)` -> 在一级缓存 `singletonObjects` 中找不到A。
    > 2.  开始实例化A：调用A的无参构造器，创建一个**原始对象**（我们称之为`aInstance`）。此时它的属性b还是null。
    > 3.  将创建A的**ObjectFactory**（对象工厂）放入**三级缓存** `singletonFactories`。这个ObjectFactory的作用是：当有人需要A的早期引用时，它可以返回这个原始对象`aInstance`（如果需要代理，则会返回一个代理对象）。
    >
    > **第二步：填充A的属性（依赖注入）**
    > 4.   Spring准备为A的属性`b`赋值，执行`getBean(B)`。
    >
    > **第三步：开始创建B**
    > 5.  `getBean(B)` -> 在一级缓存 `singletonObjects` 中找不到B。
    > 6.  开始实例化B：调用B的无参构造器，创建一个**原始对象** `bInstance`。
    > 7.  将创建B的**ObjectFactory**放入**三级缓存** `singletonFactories`。
    >
    > **第四步：填充B的属性（依赖注入）**
    > 8.  Spring准备为B的属性`a`赋值，执行`getBean(A)`。
    > 9.  关键点来了！`getBean(A)`的执行过程：
    >     *   在一级缓存 `singletonObjects` 中找不到**完整的A**。
    >     *   在二级缓存 `earlySingletonObjects` 中也找不到A。
    >     *   **但是在三级缓存 `singletonFactories` 中找到了A的ObjectFactory！**
    >     *   立刻调用这个ObjectFactory的`getObject()`方法。这个方法会返回A的早期引用（即第一步中创建的`aInstance`）。此时A虽然不完整，但地址已经确定了。
    >     *   将A的早期引用从三级缓存`singletonFactories`中**移除**，并放入**二级缓存** `earlySingletonObjects`中。
    >     *   将A的早期引用返回给B。
    >
    > **第五步：完成B的创建**
    > 10. B成功拿到了A的早期引用，并将其注入到自己的属性中。B完成了属性填充、初始化等所有步骤，变成了一个完整的Bean。
    > 11. 将完整的B放入**一级缓存** `singletonObjects`，并从二级和三级缓存中清除B的相关信息。
    > 12. B创建完毕，返回到第二步中`getBean(B)`的调用处。
    >
    > **第六步：完成A的创建**
    > 13. 此时，A成功拿到了完整的B（因为B已经在一级缓存里了），并将其注入到自己的属性中。
    > 14. A继续完成后续的属性填充、初始化等步骤，最终变成一个完整的Bean。
    > 15. 将完整的A放入**一级缓存** `singletonObjects`，并从二级和三级缓存中清除A的相关信息。
    > 16. A创建完毕，返回给用户。
    >
    > 至此，循环依赖成功解决。
    >
    > ---
    >
    > ### 4. 为什么需要三级缓存？两级不行吗？
    >
    > 这是一个非常深入的追问。关键在于 **AOP代理**。
    >
    > *   **如果Bean没有被AOP代理**：理论上，二级缓存是足够的。三级缓存中的ObjectFactory直接返回的就是原始对象，二级缓存存放的也是原始对象。
    > *   **但如果Bean被AOP代理**：问题就复杂了。Spring需要保证在整个容器中，对于同一个Bean，其代理对象是**单例**的。
    >
    > **三级缓存的核心作用是延迟代理对象的生成时机**。
    >
    > 1.  ObjectFactory的`getObject()`方法并不总是返回原始对象。它会检查是否需要AOP代理。如果需要，它会**返回一个代理对象**；如果不需要，才返回原始对象。
    > 2.  这个检查和处理代理的逻辑被封装在`SmartInstantiationAwareBeanPostProcessor`（例如 `AbstractAutoProxyCreator`）中。
    > 3.  如果只有二级缓存，那么**在Bean实例化后就必须立刻决定是返回原始对象还是代理对象**，并放入二级缓存。这破坏了Spring正常的设计原则（正常流程是在Bean初始化完成后再创建代理）。
    > 4.  而三级缓存通过ObjectFactory的方式，将“是否返回代理”的这个决策**延迟**到真正发生循环依赖、需要提前曝光的时候才执行。这保证了无论是否出现循环依赖，Bean的创建过程（包括代理的生成）都是统一的。
    >
    > **简单总结**：三级缓存的存在是为了保证在存在循环依赖且需要AOP代理的情况下，Spring仍然能正常工作，并且确保整个容器中代理对象的单例性。它是框架设计上的一致性和扩展性的体现。
    >
    > ---
    >
    > ### 总结
    >
    > | 特性         | 说明                                                         |
    > | :----------- | :----------------------------------------------------------- |
    > | **解决范围** | 单例Bean，Setter/字段注入方式的循环依赖                      |
    > | **无法解决** | 构造器注入的循环依赖、原型(Prototype)Bean的循环依赖          |
    > | **核心机制** | **三级缓存**：`singletonObjects`（一级），`earlySingletonObjects`（二级），`singletonFactories`（三级） |
    > | **关键步骤** | **提前曝光**：在Bean实例化后、填充属性前，将其ObjectFactory放入三级缓存，供其他Bean依赖引用 |
    > | **设计精髓** | 将**实例化**和**依赖注入**两个步骤分离，打破了循环依赖的“死锁”状态 |
    > | **缓存意义** | 三级缓存主要为了**兼容AOP代理**，确保代理对象生成时机的一致性和单例性 |
    >
    > 虽然Spring提供了这个强大的机制，但在实际项目中，**循环依赖通常被认为是代码设计上的问题（高耦合的体现）**，应尽量避免。