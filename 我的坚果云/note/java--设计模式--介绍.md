- https://www.w3cschool.cn/uml_tutorial/ 

  ![img](https://api2.mubu.com/v3/document_image/8209378_f9dc6339-9028-44c3-e2aa-2f10ad8606da.png)

  

- 书籍推荐

  - 《Head First 设计模式》

  - 《大话设计模式》

  - 《图解设计模式》

  - 《设计模式-可复用面向对象软件的基础》

- 设计模式七大原则

  - 单一职责原则

    - 不要存在多于一个导致类变更的原因。

    - 总结：一个类只负责一项职责。

  - 里氏替换原则

    - 1.子类可以实现父类的抽象方法，但不能覆盖父类的非抽象方法。

    - 2.子类中可以增加自己特有的方法。

    - 3.当子类的方法重载父类的方法时，方法的前置条件（即方法的形参)要比父类方法的输入参数更宽松。

    - 4.当子类的方法实现父类的抽象方法时，方法的后置条件（即方法的返回值)要比父类更严格。

    - 总结：所有引用父类的地方必须能透明地使用其子类对象

  - 依赖倒置原则/面向接口编程
    - 高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。

  - 接口隔离原则

    - 使用多个专门的接口来替代一个统一的接口；

    - 一个类对另一个类的依赖应建立在最小的接口上

  - 迪米特法则
    - 一个类对自己依赖的类知道的越少越好。也就是说，对于被依赖的类来说，无论逻辑多么复杂，都尽量地的将逻辑封装在类的内部

  - 开闭原则

    - 对扩展开放，对修改关闭

    - 用抽象构建框架，用实现扩展细节

  - 合成复用原则/组合优于继承

- 分类

  - 创建型模式，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。

  - 结构型模式，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。

  - 行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代器模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。

- 创建型

  - 工厂

    - 抽象工厂

    - 简单工厂

  - 单例
    - 多种单例模式

  - 建造者
    - 当参数比较复杂的时候

  - 原型
    - 通过clone原有对象生成新对象，避免使用new， 当创建的成本较高时候

- 结构型设计模式

  - 适配器模式
    - 将一个类的接口转换成客户所期待的另一种接口

  - 代理模式

  - 桥接模式

  - 组合模式（树形结构)

  - 享元模式

- 行为型设计模式

  - 策略模式

  - 状态模式

  - 观察者模式 发布-订阅模式。

  - 迭代器模式

  - 责任链模式

  - 命令模式

  - 备忘录模式

    - 需要保存/恢复数据的相关状态场景。 

    - 提供一个可回滚的操作。

    - 缺点：消耗资源。如果类的成员变量过多，势必会占用比较大的资源，而且每一次保存都会消耗一定的内存。

  - 访问者模式

  - 中介者模式

  - 解释器模式

- 设计模式的区分

  - 代理模式和装饰器区别

    - 装饰器模式关注于在一个对象上动态的添加方法，然而代理模式关注于控制对对象的访问。换句话说，用代理模式，代理类（proxy class)可以对它的客户隐藏一个对象的具体信息。

    - 因此，当使用代理模式的时候，我们常常在一个代理类中创建一个对象的实例。并且，当我们使用装饰器模式的时候，我们通常的做法是将原始对象作为一个参数传给装饰者的构造器。

    - 相同点：都是为被代理(被装饰)的类扩充新的功能。 

    - 不同点：代理模式具有控制被代理类的访问等性质，而装饰模式紧紧是单纯的扩充被装饰的类。所以区别仅仅在是否对被代理/被装饰的类进行了控制而已。

  - 适配器模式和代理模式的区别

    - 适配器模式，一个适配允许通常因为接口不兼容而不能在一起工作的类工作在一起，做法是将类自己的接口包裹在一个已存在的类中。

    - 装饰器模式，原有的不能满足现有的需求，对原有的进行增强。

    - 代理模式，同一个类而去调用另一个类的方法，不对这个方法进行直接操作，控制访问。

  - 抽象工厂和工厂方法模式的区别

    - 工厂方法：创建某个具体产品

    - 抽象工厂：创建某个产品族中的系列产品

    - 工厂方法模式	抽象工厂模式针对的是一个产品等级结构	针对的是面向多个产品等级结构一个抽象产品类	多个抽象产品类可以派生出多个具体产品类	每个抽象产品类可以派生出多个具体产品类一个抽象工厂类，可以派生出多个具体工厂类	一个抽象工厂类，可以派生出多个具体工厂类每个具体工厂类只能创建一个具体产品类的实例	每个具体工厂类可以创建多个具体产品类的实例

- JDK中的设计模式（17)

  - 创建型

    - 工厂方法
      - Collection.iterator() 由具体的聚集类来确定使用哪一个Iterator

    - 单例模式
      - Runtime.getRuntime()

    - 建造者模式
      - StringBuilder

    - 原型模式
      - Java中的Cloneable

  - 结构性

    - 适配器模式

      - inputStreamReader

      - OutputStreamWriter

      - RunnableAdapter

    - 装饰器模式https://segmentfault.com/a/1190000004255439

      - FileInputStream 

      - BufferedInputStream

    - 代理模式

    - 外观模式
      - java.util.logging

    - 桥接模式
      - JDBC

    - 组合模式
      - dom

    - 享元模式
      - Integer.valueOf

  - 行为型

    - 策略模式
      - 线程池的四种拒绝策略

    - 模板方法模式

      - AbstractList、AbstractMap等

      - InputStream、OutputStream

      - AQS

    - 观察者模式
      - Swing中的Listener

    - 迭代器模式
      - 集合类中的iterator

    - 责任链模式
      - J2EE中的Filter

    - 命令模式
      - Runnable、Callable，ThreadPoolExecutor

- Spring中的设计模式

  - 抽象工厂模式：
    - BeanFactory

  - 代理模式
    - AOP

  - 模板方法模式
    - AbstractApplicationContext中定义了一系列的抽象方法，比如refreshBeanFactory、closeBeanFactory、getBeanFactory。

  - 单例模式
    - Spring可以管理单例对象，控制对象为单例

  - 原型模式
    - Spring可以管理多例对象，控制对象为prototype

  - 适配器模式
    - Advice与Interceptor的适配

- 目的

  - 代码重用性

  - 可读性

  - 可扩展性

  - 使程序高内聚，低耦合

- 为了达到目的，有一些原则

  - 单一职责原则（Single Responsibility Principle）There should never be more than one reason for a class to change. 简称SRP

    - 降低类的复杂性；

    - 提高类的可读性；

    - 提高代码的可维护性和复用性

    - 降低因变更引起的风险。

  - 开闭原则(Open-Closed Principle) Software entities should be open for extension ,but closed for modification. 简称OCP
    - 一个软件实体应当对扩展开放，对修改关闭。

  - 里氏替换原则(Liskov Substitution Principle) 简称LSP

    - 代码共享，减少创建类的工作量，每个子类都拥有父类的方法和属性

    - 提高代码的可重用性；

    - 提高代码的可扩展性；

    - 提高产品或项目的开放性。

  - 接口隔离原则(Interface Segregation Principle) 简称ISP

    - Clients should not be forced to depend upon interfaces that they don't use.

    - The dependency of one class to another one should depend on the smallest possible interface。

  - 依赖倒置(Dependence Inversion Principle)，简称DIP

    - High level modules should not depend upon low level modules. 高级模块不应该依赖于低级模块。

    - Both should depend upon abstractions. 两者都应依赖于抽象。

    - Abstractions should not depend upon details. 抽象不依赖细节

    - Details should depend upon abstractions. 细节依赖抽象

- 个人看法

  - 真正开发的时候，也没必要太纠结运用哪一种设计模式，具体还是业务逻辑来决定。具体的设计模式其实还是术这个层面的东西，都是围绕五大设计原则来的。解耦合，扩展性，修改关闭，就像武侠剧里面说的，当你忘掉了所有的招式，你就学会太极拳了。有道无术术尚可求，有术无道止于术。