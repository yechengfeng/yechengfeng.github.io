- Java 8 / Java SE 8 (2014) - LTS:

  - 函数式编程革命:

    - Lambda 表达式 ((x, y) -> x + y): 核心变革，简化匿名内部类。

    - Stream API: 声明式、链式、并行的数据处理 (filter, map, reduce, collect 等)。

    - 函数式接口 (@FunctionalInterface) & java.util.function 包: Predicate, Function, Consumer, Supplier 等。

    - 默认方法 (default) 和静态方法 (static) in Interfaces: 接口演化能力。

    - 方法引用 (System.out::println): Lambda 的语法糖。

    - Optional<T>: 优雅处理 null。

    - 新的日期时间 API (java.time): LocalDate, LocalDateTime, ZonedDateTime, Duration, Period (替代 Date/Calendar)。

    - Nashorn JavaScript 引擎: 替代 Rhino。

    - 元空间 (Metaspace): 替代永久代 (PermGen)，解决 OOM 问题。

    - CompletableFuture: 强大的异步编程工具。

    - 重复注解 (@Repeatable): 同一位置多次使用同一注解。

    - 类型注解 (@NonNull String): 注解可用于更多地方（需配合工具）。

- Java 9 / Java SE 9 (2017):

  - 模块化 (Project Jigsaw):

    - Java 平台模块系统 (JPMS): 引入 module-info.java，定义模块依赖和导出。解决 JAR Hell，提升安全性、可维护性和性能。

    - JLink: 创建只包含所需模块的自定义运行时镜像。

  - JShell: 交互式 REPL (Read-Eval-Print Loop) 工具。

  - 集合工厂方法 (List.of(), Set.of(), Map.of()): 创建不可变集合。

  - 改进的 Stream API: takeWhile, dropWhile, iterate 增强。

  - 私有接口方法: 接口内允许 private 方法。

  - HTTP/2 Client (孵化器): 现代 HTTP 客户端 API (标准版在 Java 11)。

  - 多版本兼容 JAR: 支持同一个 JAR 包含不同 Java 版本的类文件。

  - 进程 API 增强: 改进进程管理和信息获取。

- Java 10 / Java SE 10 (2018):

  - 局部变量类型推断 (var): var list = new ArrayList<String>(); (编译时推断，仍是强类型)。

  - G1 并行 Full GC: 提升 G1 垃圾收集器在 Full GC 时的性能。

  - 应用类数据共享 (AppCDS): 提升启动速度，减少内存占用。

  - 线程局部管控: 允许停止单个线程（用于调试等场景）。

- Java 11 / Java SE 11 (2018) - LTS:

  - var 可用于 Lambda 参数: (var x, var y) -> x + y (需要显式类型时)。

  - HTTP Client (标准): java.net.http 包，支持 HTTP/1.1, HTTP/2, WebSocket。

  - Files 读写字符串: readString(), writeString() 简化文件读写。

  - String 增强: isBlank(), lines(), repeat(), strip() 等。

  - Optional.isEmpty(): 检查 Optional 是否为空。

  - Epsilon GC: 无操作 GC，用于性能测试等特殊场景。

  - ZGC (实验): 可扩展的低延迟 GC (目标暂停时间 < 10ms)。

  - 移除内容:

    - Java EE 模块 (CORBA, JTA, JAXB, JAX-WS 等 - 移至 Jakarta EE)。

    - JavaFX (分离成独立模块)。

    - Nashorn JavaScript 引擎 (标记废弃)。

    - Java Web Start / Applet API (标记废弃)。

- Java 12 - 16 (非 LTS, 2019 - 2021): 快速迭代引入预览特性

  - switch 表达式 (预览 -> 标准): yield 返回值，-> 箭头语法 (J12 预览, J14 二次预览, J15 标准)。

  - 文本块 (预览 -> 标准): """...""" 简化多行字符串 (J13 预览, J14 二次预览, J15 标准)。

  - instanceof 模式匹配 (预览): if (obj instanceof String s) { s.length(); } (J14 预览, J15 二次预览, J16 标准)。

  - Records (预览 -> 标准): record Point(int x, int y) {} 简化不可变数据载体类 (J14 预览, J15 二次预览, J16 标准)。

  - Sealed Classes (预览): permits 关键字限制哪些类可以继承/实现 (J15 预览, J16 二次预览, J17 标准)。

  - Shenandoah GC (实验 -> 标准): 低暂停时间 GC (J12 实验, J15 标准)。

  - ZGC (实验 -> 标准): (J11 实验, J15 标准)。

  - jpackage (孵化 -> 标准): 打包独立应用 (J14 孵化, J16 标准)。

- Java 17 / Java SE 17 (2021) - LTS:

  - **Sealed Classes (标准): 正式成为标准特性。**

  - Pattern Matching for switch (预览): **switch 支持类型模式匹配和 null (J17 预览)。**

  - **移除实验性 AOT/JIT**: 移除 GraalVM 相关的实验性特性。

  - 恢复严格浮点语义: 默认恢复 strictfp 语义。

  - **强封装 JDK 内部 API: 默认禁止通过反射访问关键内部 API (如 sun.misc.Unsafe)，需要使用 --add-opens。**

  - 上下文特定的反序列化过滤器: 增强反序列化安全性。

  - 其他: 移除 Applet API、Security Manager 标记废弃、jpackage 成为标准工具等。当前企业主流升级目标。

- Java 18 - 21 (2022 - 2023):

  - switch 模式匹配 (标准): (J21 标准)。

  - Record Patterns (预览 -> 标准): if (o instanceof Point(int x, int y)) { ... } (J19 预览, J20 预览, J21 标准)。

  - 虚拟线程 (预览 -> 标准): Thread.startVirtualThread(() -> {...}) 轻量级线程，简化高吞吐并发 (J19 预览, J20 预览, J21 标准)。

  - 结构化并发 (孵化 -> 预览): StructuredTaskScope 简化管理相关任务 (J19 孵化, J21 预览)。

  - Vector API (孵化): 表达向量计算，利用 SIMD 指令 (持续孵化)。

  - Foreign Function & Memory API (孵化 -> 预览): 安全高效地访问非 Java 内存和调用本地代码 (替代 JNI) (J19 孵化, J20 预览, J21 预览)。

  - 字符串模板 (预览): STR."Hello \{name}!" (J21 预览)。

  - 分代 ZGC: ZGC 支持分代回收，提升效率 (J21)。

  - 记录模式增强 (预览): 嵌套记录模式等 (J21 预览)。

  - 未命名模式和变量 (_): 忽略不需要的模式或变量 (J21 预览)。

  - main 方法简化: 允许不写 public static void (J21 预览)。

  - 作用域值 (预览): 在线程内和子线程间共享不可变数据，替代 ThreadLocal (J21 预览)。

-  总结关键差异趋势

  - 语言演进: 从纯粹的面向对象 -> 引入泛型 -> 引入函数式编程 (Lambda, Stream) -> 引入数据载体 (Record) -> 引入模式匹配 (instanceof, switch) -> 引入更安全的并发 (虚拟线程, 结构化并发) -> 简化语法 (var, main 简化)。

  - API 增强: 集合框架 -> NIO -> 并发工具 (java.util.concurrent) -> 新日期时间 (java.time) -> HTTP Client -> Optional -> Stream API。

  - 性能与内存: JIT -> G1 GC -> ZGC/Shenandoah (低延迟) -> 分代 ZGC -> 元空间 (替代 PermGen) -> AppCDS。

  - 模块化与封装: JPMS (Java 9) -> 强封装内部 API (Java 16/17)。

  - 安全性: 移除不安全/过时特性 (Applet, CORBA, Java Web Start, Security Manager), 增强反序列化安全。

  - 现代化: 移除 EE 模块 (移至 Jakarta EE), 分离 JavaFX, 移除 Nashorn。

  - 开发体验: JShell (REPL), var, 文本块, 集合工厂方法, jpackage, 改进的 switch/instanceof。

  - 发布节奏: Java 9 开始，每 6 个月一个版本。每 3 年一个 LTS 版本 (长期支持)。主流 LTS: Java 8, Java 11, Java 17, Java 21。

- 当前建议

  - 新项目: 强烈推荐使用 Java 17 或 Java 21 (最新 LTS)。

  - 现有项目升级: 从 Java 8 或 11 升级到 Java 17 是目前最主流的选择，Java 21 也逐渐成熟。

  - 学习: 学习 Java 17 或 Java 21 的特性是必要的，同时了解 Java 8 的核心特性 (Lambda, Stream) 仍是基础。

- Java 的发展始终围绕着提高开发效率、提升性能、增强安全性、拥抱现代编程范式和简化开发体验。了解这些版本的差异有助于选择合适的平台并理解 Java 的进化方向。

- java21我觉得新特性最重要的zgc实现了垃圾回收的优化以及携程，还有模式匹配

- JAVA_8

  - lambda 表达式 简化匿名内部类

  - stream API

  - 接口默认方法

  - JAVA Time 替代Calendar Date

    - localdate

    - LocalDateTime ZoneDateTime

  - Optional : 优雅处理null

  - CompletabelFuture

  - 元空间(MetaSpace)替换掉了永久代 解决了OOM 问题