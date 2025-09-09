> - ### 接口层：
>
>   - MyBatis和数据库的交互有两种方式
>
>     - a.使用传统的MyBatis提供的API；
>
>     - b. 使用Mapper接口
>
> - ### 数据处理层：
>
>   - a. 通过传入参数构建动态SQL语句；
>
>   - b. SQL语句的执行以及封装查询结果集成List<E>
>
> - ### 框架支撑层：
>
>   - MyBatis的主要的核心部件有以下几个：
>
>     - SqlSession            作为MyBatis工作的主要顶层API，表示和数据库交互的会话，完成必要数据库增删改查功能
>
>     - Executor              MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护
>
>     - StatementHandler   封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数、将Statement结果集转换成List集合。
>
>     - ParameterHandler   负责对用户传递的参数转换成JDBC Statement 所需要的参数，
>
>     - ResultSetHandler    负责将JDBC返回的ResultSet结果集对象转换成List类型的集合；
>
>     - TypeHandler          负责java数据类型和jdbc数据类型之间的映射和转换
>
>     - MappedStatement   MappedStatement维护了一条<select|update|delete|insert>节点的封装， 
>
>     - SqlSource            负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回
>
>     - BoundSql             表示动态生成的SQL语句以及相应的参数信息
>
>     - Configuration        MyBatis所有的配置信息都维持在Configuration对象之中。
>
> - ### 初始化过程
>
>   - MyBatis初始化的过程，就是创建 Configuration对象的过程。
>
>   - MyBatis的初始化可以有两种方式：
>
>     - 基于XML配置文件：基于XML配置文件的方式是将MyBatis的所有配置信息放在XML文件中，MyBatis通过加载并XML配置文件，将配置文信息组装成内部的Configuration对象
>
>     - 基于Java API：这种方式不使用XML配置文件，需要MyBatis使用者在Java代码中，手动创建Configuration对象，然后将配置参数set 进入Configuration对象中。
>
>     - mybatis初始化 -->创建SqlSession -->执行SQL语句
>
>     - SqlSessionFactoryBuilder根据传入的数据流生成Configuration对象，然后根据Configuration对象创建默认的SqlSessionFactory实例。
>
>       - 调用SqlSessionFactoryBuilder对象的build(inputStream)方法；
>
>       - SqlSessionFactoryBuilder会根据输入流inputStream等信息创建XMLConfigBuilder对象;
>
>       - SqlSessionFactoryBuilder调用XMLConfigBuilder对象的parse()方法；
>
>       - XMLConfigBuilder对象返回Configuration对象；
>
>       - SqlSessionFactoryBuilder根据Configuration对象创建一个DefaultSessionFactory对象；
>
>       - SqlSessionFactoryBuilder返回 DefaultSessionFactory对象给Client，供Client使用。
>
> - ### 数据源与连接池
>
>   - MyBatis把数据源DataSource分为三种：
>
>   - UNPOOLED    不使用连接池的数据源
>
>   - POOLED      使用连接池的数据源
>
>   - JNDI            使用JNDI实现的数据源
>
> - #### 一级缓存（Session级别的缓存)
>
>   - 每当我们使用MyBatis开启一次和数据库的会话，MyBatis会创建出一个SqlSession对象表示一次数据库会话。
>
>   - 在对数据库的一次会话中，我们有可能会反复地执行完全相同的查询语句，如果不采取一些措施的话，每一次查询都会查询一次数据库,而我们在极短的时间内做了完全相同的查询，那么它们的结果极有可能完全相同，由于查询一次数据库的代价很大，这有可能造成很大的资源浪费。
>
>   - 为了解决这一问题，减少资源的浪费，MyBatis会在表示会话的SqlSession对象中建立一个简单的缓存，将每次查询到的结果结果缓存起来，当下次查询的时候，如果判断先前有个完全一样的查询，会直接从缓存中直接将结果取出，返回给用户，不需要再进行一次数据库查询了。
>
>   - 当创建了一个SqlSession对象时，MyBatis会为这个SqlSession对象创建一个新的Executor执行器，而缓存信息就被维护在这个Executor执行器中，MyBatis将缓存和对缓存相关的操作封装成了Cache接口中。SqlSession、Executor、Cache之间的关系如下列类图所示：
>
>   - Executor接口的实现类BaseExecutor中拥有一个Cache接口的实现类PerpetualCache，则对于BaseExecutor对象而言，它将使用PerpetualCache对象维护缓存。
>
>   - Perpetual Cache（永久的)
>
>   - PerpetualCache实现原理其实很简单，其内部就是通过一个简单的HashMap<k,v> 来实现的，没有其他的任何限制。
>
>   - 生命周期
>
>     - a. MyBatis在开启一个数据库会话时，会 创建一个新的SqlSession对象，SqlSession对象中会有一个新的Executor对象，Executor对象中持有一个新的PerpetualCache对象；当会话结束时，SqlSession对象及其内部的Executor对象还有PerpetualCache对象也一并释放掉。
>
>     - b. 如果SqlSession调用了close()方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用；
>
>     - c. 如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用；
>
>     - d.SqlSession中执行了任何一个update操作(update()、delete()、insert()) ，都会清空PerpetualCache对象的数据，但是该对象可以继续使用；
>
> - #### 二级缓存（Mapper级别的缓存)
>
> - MyBatis与Hibernate比较
>
> - Spring对SqlSession的管理
>
> - 动态代理SqlSessionFactory
>
> ### 参考资料
>
> ​		[https://javaguide.cn/system-design/framework/mybatis/mybatis-interview.html#%E5%92%8C-%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88](https://javaguide.cn/system-design/framework/mybatis/mybatis-interview.html#和-的区别是什么)
>
> - https://javabetter.cn/sidebar/sanfene/mybatis.html
>
> - MyBatis中#{}和${}区别(*)
>
>   - \#{}是经过预编译的,是安全的,而${}是未经过预编译的,仅仅是取变量的值,是非安全的,存在sql注入。
>
>   - 只能＄{}的情况,order by、like 语句只能用＄{}了,用#{}会多个' '导致sql语句失效.此外动态拼接sql也要用${}
>
>   - \#{} 这种取值是编译好SQL语句再取值, ${} 这种是取值以后再去编译SQL语句