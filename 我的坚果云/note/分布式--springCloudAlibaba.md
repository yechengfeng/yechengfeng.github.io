 系统构建标准

> - nacos/zookeeper
>   - 服务治理 注册中心，服务发现 剔除 
>   - 可以提供统一的配置文件 ，类似Apollo 
>     - 如果需要实时的刷新，可以利用**RefreshScope**注解进行刷新
> - loadbalancer
>   - 负载均衡器
>   - https://www.cnblogs.com/wzh2010/p/18031153
>   
> - feign
>   - 服务之间通讯
>
> - geteway
>   - 统一的网关
>
> - sentinel
>   - 容错 限流
> - seata
>   - 保证事务的一致性
> - RocketMQ

### Gateway(网关)

> - 所谓的API网关，就是指系統的統一入口，它封装了应用程序的内部结构，为客户端提供统一服务，所谓的API网关，就是指系統的統一入口，它封装了应用程序的内部结构，为客户端提供统一服务，
> - Spring Cloud Gateway 是由 WebFlUx + Netty + Reactor 实现的响应式的AP1 网关。它不能在传统的servlet 容器中工作，也不能构建成war包。
>
> 

1. 全局性的流控
2. 日志统计
3. 防止sql注入
4. 防止web攻击
5. 屏蔽工具扫描
6. 黑白名单
7. 证书/加解密处理
8. 提供级别流控
9. 服务降级和熔断
10. 路由和负载均衡，灰度策略
11. 服务过滤，聚合发现
12. 权限验证和用户等级策略
13. 业务规则和参数校验
14. 多级缓存策略

配置

```yaml
cloud:
	getway:
	 rootes:
	     id: order_roote  路由的唯一标识 ，路由到order
	     uri: http://localhost:8080 #需要转发的地址
	     predicates:#断言规则，用于路由规则的匹配
	     filters:
	      -stripPrefix =1 转发之前去掉第一层的路径
	      
```



