- BeanFactoryPostProcessor
  
  - **BeanPostProcessor**
  
    - bean的后置处理器,
  
  - **BeanFactoryPostProcessor**
  
    - beanFactory的后置处理器，在beanFactory标准初始化之后调用，所有的bean定义已经保存加载到beanFactory中，但是bean的实例还没有创建
  
    > 1. IOC容器创建对象
    > 2. invokeBeanFactoryFactoryPost
  
- BeanDefinitionRegistryPostProcessor 
  
  ```java
  //在所有的bean定义信息要被加载，bean实例还未创建的 
  interface BeanDefinitionRegistryPostProcessor extends BeanFactoryPostProcessor{
    	void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException;
  }
  ```
  
  

