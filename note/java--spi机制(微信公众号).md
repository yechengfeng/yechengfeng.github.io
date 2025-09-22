- [https://www.seven97.top/java/basis/06-SPI.html#spi%E6%9C%BA%E5%88%B6%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86](https://www.seven97.top/java/basis/06-SPI.html#spi机制实现原理)
- [https://javaguide.cn/java/basis/spi.html](https://javaguide.cn/java/basis/spi.html)

## SPI 的概念

**SPI = Service Provider Interface**，中文通常称为 **服务提供者接口**。

- 它是一种 **接口和实现解耦的机制**
- 允许在运行时动态加载不同的实现
- 常用于框架提供可扩展能力，让开发者 **替换或扩展实现**

> 简单理解：SPI 就是告诉框架“我提供了这个接口的实现，你可以按需加载它”，而不是在编译期固定实现。

------

## Java 中的 SPI 实现机制

Java 的 SPI 机制是基于 **`java.util.ServiceLoader`** 的：

### (1) 定义接口

```java
public interface MyService {
    void execute();
}
```

### (2) 提供实现

```java
public class MyServiceImpl implements MyService {
    @Override
    public void execute() {
        System.out.println("SPI implementation executed!");
    }
}
```

### (3) 配置 META-INF/services

在 jar 包里创建文件：

```
META-INF/services/com.example.MyService
```

内容为实现类全限定名：

```
com.example.MyServiceImpl
```

### (4) 加载服务

```java
ServiceLoader<MyService> loader = ServiceLoader.load(MyService.class);
for (MyService service : loader) {
    service.execute();
}
```

- JVM 会扫描 `META-INF/services`，加载对应实现类
- 可以有多个实现类 → 框架可按需选择

------

## SPI 的特点

| 特点     | 说明                                     |
| -------- | ---------------------------------------- |
| 动态加载 | 不需要在代码里写死具体实现               |
| 解耦     | 框架只依赖接口，不依赖实现               |
| 可扩展   | 用户或第三方 jar 可以提供新的实现类      |
| 多实现   | 一个接口可以有多个实现，按顺序或条件加载 |

------

##  SPI 的应用场景

1. **Java 标准库**
   - JDBC：`DriverManager` 通过 SPI 加载数据库驱动
   - JDK 里面的 `java.util.logging`
2. **开源框架**
   - Spring Boot：`spring.factories` 实现自动配置
   - Dubbo：SPI 加载不同协议、序列化方式、负载均衡策略
3. **自定义插件化框架**
   - 用户实现接口 → 框架自动扫描 jar 包 → 插件注册

------

## 注意事项

1. **META-INF/services 文件必须存在**，否则 ServiceLoader 无法发现实现
2. **实现类必须有无参构造函数**，SPI 通过反射实例化
3. **如果有多个实现**，可以用排序（`@Order`）或 `java.util.Iterator` 控制加载顺序
4. **Spring 扩展的 SPI**（如 Dubbo）增加了 `Adaptive` 注解，可以按条件选择实现，功能更强

------

总结：

- **SPI 是接口与实现解耦的机制**
- **基于 ServiceLoader + META-INF/services 文件**
- 常用于 **插件化、可扩展框架、动态加载实现**

