- https://www.bilibili.com/video/BV1BV4y1Q79F?spm_id_from=333.788.player.switch&vd_source=8db2bf2ffb3ab22965fccab48d4389ba&p=19

  

##### 介绍

> - introduce
>
>   - spring batch 是一个批处理的框架 ，批处理就是将数据分批处理的过程，比如银行对账逻辑，跨系统数据同步问题
>
>   - 常规的系统A从数据库导出数据到文件，系统B读取文件数据并且写入到数据库
>
> - 架构
>
>   - JobLauncher :作业调度器
>
>   - Job :作业需要执行的任务逻辑
>
>     - job可以理解为一段业务流程的实现，可以根据业务流程拆分一个或者多个step，然后业务逻辑顺序执行
>
>     - JobInstance 作业实例（job名称+识别参数 ）
>       - springbatch中相同的名称和参数只能执行一次
>
>     - JobExecution  作业执行器
>
>   - Step: 作业步骤，一个job由多个step组成，完成所有的step，一个完整的Job才算执行结束
>
>   - ItemReader: step执行过程中数据的输入
>
>   - ItemWrite: step执行过程中数据输出
>
>   - ItemProcesser ：Item数据加工逻辑，比如数据清洗，数据转换，数据过滤，数据校验
>
>   - JobRepository：保存job或者检索job的信息，springBatch需要持久化JOB，数据库或者内存，JobRepository就是持久化的接口
>
> - 使用
>
>   - 创建需要的表 sql已经提供，只需要引用就可以，配置sql.init.schema-locations参数为对应数据库的sql创建脚本，会自动创建9张表。sql.init.model第一次配置为always，之后为never
>
>   - .EnableProcessing 注解配置，会自动加载JobLuncher jobBuilderFactory,StepBuilderFactory类并且交给容器管理，直接Autowired使用即可
>
>   - 对于Tasklet模式创建Tasklet，是step的一个步骤，方法需要返回一个状态，如果是complete，就是执行完成，continue，会重复执行这个step
>
>   - 创建Job对象交给容器管理，当springBoot启动之后，会自动从容器中加载Job对象，并且将Job交给JobLuncherApplicationRunner管理，再借助JobLauncher类实现Job执行
>
>   - 对于Chunk块模式,
>
> - 作业的参数对象
>
>   - JobParameters
>     - JobParameters类底层维护了Map<String,JobParameter>,是Map集合的封装
>
> - springbatch参数获取方式
>
>   - 1.ChunkContext类 
>
>   - 2.value 注解获取
>
>     - 在tasklet中使用#{jobParameters['name']} 这种方式获取
>
>     - 在tasklet bean上加注解stepscope，意思是这个bean在step用到的时候再延迟加载
>
> - springbatch参数校验
>
>   - 实现JobParametersValidator 接口
>     - 实现接口，如果不符合逻辑，抛出参数异常
>
>   - 默认的参数校验器
>
>     - DefaultJobParametersValidator 默认的参数校验器
>       - 维护了俩个key集合 ，一个optionalKey,一个requiredKeys
>
>     - CompositeJobParametersValidator 参数组合校验器
>
> - 作业增量参数
>
>   - 接口 JobParametersIncrementer ，springBatch有一个实现类，RunIdIncrementer,每次修改runID,所以一个Job通过修改参数的方式可以run多次
>
>   - 但是RUNID通常在业务中没有实际意义，所以可以自定义增量器，可以把时间戳作为参数增量器的参数。
>
> - 作业监听器
>
>   - 用于监听作业的执行过程的逻辑。在**作业执行前，执行后2个时间点嵌入业务逻辑**
>
>     - 执行前:一般用于初始化操作，作业执行前需要着手准备工作，比如各种连接，线程池初始化
>
>     - 执行后：业务执行完成后，需要各种清理工作，比如释放资源
>
>   - springBatch使用JobExecutionListener接口实现作业监听
>
>     - 使用接口的方式 extend JobExecutionListener
>
>     - 使用注解的方式
>
>       - beforeJob
>
>       - afterJob
>
> - 执行上下文
>
>   - JobContext
>     - 为Job作业执行提供上下文执行环境，维护JobExecution对象，实现作业收尾工作，处理作业的回调逻辑
>
>   - StepContext
>     - 绑定StepExecution对象，实现步骤收尾工作，处理各种步骤回调逻辑
>
>   - ExecutionContext
>
>     - 作用是数据共享 ，分为俩大类
>
>       - Job ExecutionContext
>         - 作用域: 一次作业运行,所有step间数据共享
>
>       - Step ExecutionContext
>         - 作用域：一次步骤执行，单个step间(ItemReader/ItemProcessor/ItemWrite组件间)数据共享
>
> - 作业和步骤引用链
>
>   - 作业线
>     - Job-JobInstance--JObContext--JobExecution--ExecutionContext
>
>   - 步骤线
>     - Step--StepContext--StepExecution-ExecutionContext
>
> - 上下文对象常用API
>
>   - ChunkContext获取步骤上下文，执行上下文
>
>   - JobsynchronizationManager 获取作业上下文