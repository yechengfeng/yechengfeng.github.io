- 原理

  - EnableTransactionManagement 注解

    - 利用import给容器中导入俩个组件TransactionManagementConfigurationSelector

      - AutoProxyRegistrar

        - 父类: ImportBeanDefinitionRegistrar

        - 给容器导入了 InfrastructureAdvisorAutoProxyCreator组件

          - 利用后置处理器机制在对象创建之后，包装对象，返回一个代理对象（增强器），代理对象在执行方法前后拦截。

          - InfrastructureAdvisorAutoProxyCreator的集成关系可以结合之前的AnnotationAwareAspectJAutoProxyCreator，逻辑基本一样

      - ProxyTransactionManagementConfiguration