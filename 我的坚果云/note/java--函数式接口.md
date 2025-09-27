- https://www.jianshu.com/p/0c07597d8311

- https://www.jianshu.com/p/9bd647bcf1e3

- 函数式接口

  - 函数式接口是只定义了一个抽象方法的接口。注意Java8中允许存在默认方法（default），哪怕有很多默认方法，只要有且仅有一个抽象方法，那么这个接口仍然是函数式接口。

- 常用函数式接口

  ![img](https://api2.mubu.com/v3/document_image/8209378_a37bc197-e9a9-465c-c3b1-3e99bd0880bb.png)

- 从Java8开始引入了一个新类 java.util.Optional解决空指针

  > ```java
  > Optional<Person> optionalPerson = Optional.Empty();//创建一个空对象
  > 
  > Optional<Person> optionalPerson = Optional.of(person);
  > //常用方法
  >  - ifPresent //如果值存在，则调用consumer实例消费该值，否则什么都不执行
  >  Optional.ofNullable(str).ifPresent(System.out::println);
  >  - filter, map, flatMap
  > 
  > - orElse //如果value为空，则返回默认值，
  >   
  > - orElseGet //如果value为空，则调用Supplier实例返回一个默认值。
  >  
  > - orElseThrow //如果value为空，则抛出自定义异常。
  > 
  >   
  > ```

- 创建Stream实例的方法

  - （1）使用指定值创建Stream实例

    - Stream<String> strStream = Stream.of("hello", "java8", "stream");

    - IntStream intStream = IntStream.of(1, 2, 3);

  - （2）使用集合创建Stream实例（常用方式）

    - ImmutableList<Integer> integers = ImmutableList.of(1, 2, 3);// 使用guava库，初始化一个不可变的list对象

    - Stream<Integer> stream =[integers.stream](http://integers.stream/)() // List接口继承Collection接口，java8在Collection接口中添加了stream方法

  - （3）使用数组创建Stream实例

    - Integer[] array = {1, 2, 3};// 初始化一个数组

    - Stream<Integer> stream = [Arrays.stream](http://arrays.stream/)(array);// 使用Arrays的静态方法stream

  - （4）使用生成器创建Stream实例

    - Random random = new Random();// 随机生成100个整数

    - Stream<Integer> stream = Stream.generate(random::nextInt).limit(100);// 加上limit否则就是无限流了

  - （5）使用迭代器创建Stream实例
    - Stream<Integer> stream = Stream.iterate(1, n -> n + 2).limit(100);

  - （6）使用IO接口创建Stream实例
    - Stream<Path> pathStream = Files.list(Paths.get("/"));

- Stream常用操作

  ![img](https://api2.mubu.com/v3/document_image/8209378_1517c3e9-d7c5-4357-81ab-6c162430061e.png)

- （1）中间操作

  - 中间操作会返回另外一个流，多个中间操作可以连接起来形成一个查询。中间操作有惰性，如果流上没有一个终端操作，那么中间操作是不会做任何处理的。

    - map操作
      - map是将输入流中每一个元素映射为另一个元素形成输出流。

    - flatMap
      - flatMap将流中每个元素取出来转成另外一个输出流

    - filter操作
      - filter接收Predicate对象，按条件过滤，符合条件的元素生成另外一个流。

  - （2）终端操作
    - foreach, min, max, count等。

-  使用Stream常见的误区

  - （1）误区一：重复消费stream对象
    - stream对象一旦被消费，不能再次重复消费。

  - （2）误区二：修改数据源
    - 注意：一定不要在操作流的过程中修改数据源。

- 一 函数式接口

  - 函数式接口是只定义了一个抽象方法的接口。注意Java8中允许存在默认方法（default），哪怕有很多默认方法，只要有且仅有一个抽象方法，那么这个接口仍然是函数式接口。

- 二 Java 8 内置的四大核心函数式接口

  - Java 在 java.util.function 包中提供了丰富的内置函数式接口：

    - Consumer<T>：消费者（接收参数无返回）
      - void accept(T t);  // 消费一个参数

    - Supplier<T>：供应者（无参数有返回）
      - T get();  // 提供结果

    - Function<T， R>：函数（接收参数有返回）
      - R apply(T t);  // 转换输入为输出

    - predicate<T>：断言（接收参数返回布尔）
      - boolean test(T t);  // 测试条件

- Stream

  - 特点

    - 只能遍历（消费）一次。Stream实例只能遍历一次，终端操作后一次遍历就结束，再次遍历需要重新生成实例，这一点类似于Iterator迭代器。

    - 保护数据源。对Stream中任何元素的修改都不会导致数据源被修改，比如过滤删除流中的一个元素，再次遍历该数据源依然可以获取该元素。

    - 懒。filter, map 操作串联起来形成一系列中间运算，如果没有一个终端操作（如collect）这些中间运算永远也不会被执行。