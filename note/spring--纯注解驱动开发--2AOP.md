-  AOP原理

  - **EnableAspectJAutoProxy 注解**

    - 通过@Import(AspectJAutoProxyRegistrar.class) ，利用AspectJAutoProxyRegistrar自定义给容器中注入bean;给容器中注册一个internalAutoProxyCreator=AnnotationAwareAspectJAutoProxyCreator

    - AspectJAwareAdvisorAutoProxyCreator

      - 父类: AbstractAdvisorAutoProxyCreator

        - 父类: AbstractAutoProxyCreator

          - 父类: ProxyProcessorSupport 

          - 父类:SmartInstantiationAwareBeanPostProcessor

            - 父类：InstantiationAwareBeanPostProcessor (作用，每一个bean创建之前调用，postProcessBeforeInstantiation方法)
              - 父类：BeanPostProcessor（后置处理器，bean完成初始化之后做的事情）

            - 父类：BeanFactoryAware
              - ![img](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250909-134059-8209378_14ed4ae3-3689-466e-b2ba-a5e27c16a641.png)

      - 流程

        1. 传入配置类，创建IOC容器

        2. 注册配置类，调用refresh().刷新容器

        3. registerBeanPostProcessors(beanFactory),注册bean的后置处理器来方便拦截bean的创建

           1. 先获取IOC容器已经定义了的需要创建对象的所有BeanPostProcessor
           2. 给容器中加入别的BeanPostProcessor
           3. 优先注册实现了PriorityOrdered接口的BeanPostProcessor;
           4. 再给容器中注册实现了ordered接口的BeanPostProcessor;
           5. 再注册没实现优先级的BeanPostProcessor
           6. 注册BeanPostProcessor,实际上就是创建BeanPostProcessor对象，保存在容器中。
              - 创建internalAutoProxyCreator =AnnotationAwareAspectJAutoProxyCreator
                1. 创建Bean的实例
                2. populateBean，给bean的属性赋值
                3. .initializeBean : 初始化bean
                   1. invokeAwareMethods :Aware接口的方法回调
                   2. applyBeanPostProcessorsBeforeInitialization 后置处理器的前置调用
                   3. invokeInitMethods 执行初始化方法
                   4. applyBeanPostProcessorsAfterInitialization执行后置处理器的后置调用
                4. BeanPostProcessor AnnotationAwareAspectJAutoProxyCreator创建成功
           7. 把BeanPostProcessor注册到BeanFactory中

        4. finishBeanFactoryInitialization(beanFactory); 完成beanFactory的初始化工作，创建剩下的单实例bean

           1. 遍历获取容器中所有的bean，依次创建对象getBean(beanName);

              - getBean->doGetBean()->getSingleton()->

           2. 创建bean

              1. 先从缓存中获取bean，如果可以获取到，说明被创建过，直接使用，否则创建

              2. createBean

                 1. resolveBeforeInstantiation(beanName, mbdToUse);

                    - 希望后置处理器可以在此返回一个代理对象，如果可以返回代理对象就是用，如果不能就继续

                      - 1.后置处理器先尝试返回对象

                      - ```java
                        bean = applyBeanPostProcessorsBeforeInstantiation(targetType, beanName); //如果是InstantiationAwareBeanPostProcessor后置处理器，那么就调用InstantiationAwareBeanPostProcessor的前置拦截方法。
                        if (bean != null) {
                          bean = applyBeanPostProcessorsAfterInitialization(bean, beanName);
                        }

                 2. doCreateBean(beanName, mbdToUse, args); （和3.6一样）

      - 分析AnnotationAwareAspectJAutoProxyCreator（InstantiationAwareBeanPostProcessor）

        每一个bean创建之前调用，postProcessBeforeInstantiation方法

        1. 判断当前bean是否在adviseBean中（保存了所有需要增强的bean）
        2. 判断当前bean是否是基础类型isInfrastructureClass
           - 是否是Advice, PointCut, advisor, AopInfrastructureBean, Aspect
        3. 是否需要跳过，通过isOriginalInstance判断

      - postProcessAfterInitialization

        - return this.wrapIfNecessary(bean, beanName, cacheKey);

          1. 获取当前bean的所有增强器（通知方法），找到可以在当前bean使用的增强器给增强器排序

          2. 保存当前bean到adviseBean

          3. 如果当前bean需要增强创建当前bean的代理对象

             - 1.获取所有的增强器（通知方法）

             - 2.保存到proxyFactory

             - 3.创建代理对象，

               - JDK动态代理 有接口就用jdk

               - cglib动态代理 ，

          4. 给容器中返回当前组件的代理对象。

  - 目标方法执行

    - 容器中保存了组件的代理对象(CGlib代理对象)这个对象保存了增强器，目标对象

      - 1.CglibAopProxy.intercept()；拦截目标方法的执行

      - 2.根据ProxyFactory获取拦截器链Chain

        -  this.advised.getInterceptorsAndDynamicInterceptionAdvice(method, targetClass);

          - 创建一个interceptorList，长度为advisors的长度

          - 遍历所有的增强器，将其转化成Interceptor getInterceptors()
            - 如果是MethodInterceptor直接加入到集合中，如果不是 使用AdvisorAdapter转换成MethodInterceptor

      - 3.如果没有拦截器链，直接执行目标方法

      - 4.如果有拦截器链，把需要执行的目标对象，目标方法。拦截器链等信息传入一个ReflectiveMethodInvocation ，调用proceed()方法

      - 5.拦截器链的触发过程ReflectiveMethodInvocation.proceed()

        - 如果没有拦截器 直接执行目标方法

        - 链式获取每一个拦截器，拦截执行invoke方法，每一个拦截器等待下一个拦截器执行完成返回以后再来执行。

  - AOP原理总结：

    - EnableAspectJAutoProxy开启AOP功能

    - EnableAspectJAutoProxy会给容器祖册AOP相关的组件实例AnnotationAwareAspectJAutoProxyCreator，AnnotationAwareAspectJAutoProxyCreator本质是一个后置处理器

    - 容器创建流程 

      - registerBeanPostProcessors() ;会注册后置处理器，创建AnnotationAwareAspectJAutoProxyCreator对象到容器中

      - finishBeanFactoryInitialization();创建剩下的单实例bean，

        - 创建业务逻辑组件和切面组件

        - AnnotationAwareAspectJAutoProxyCreator会拦截组件的创建过程

        - 组件创建完成之后，判断组件是否需要增强， 切面的通知方法，包装成增强器(Advisor);

    - 执行目标方法

      - 代理对象执行目标方法

      - .CglibAopProxy.intercept()
        - 得到目标的所有拦截器链，依次执行