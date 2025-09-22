1. why securtiy

   - 权限管理 shiro(扩展性不强，微服务支持不好）,自定义，spring security  （spring cloud security 学习成本比shiro 高)

      - 认证 (登录)

      - 授权

   - security，扩展性强，容错性高，一站式解决跨域问题，授权认证问题，包括权限部分，都可以很好的解决，所以我们用了security来做安全框架。

   - 现在的打包方式，每一个模块都可以单独部署，可以很好的过渡到微服务。现在是打成一个war包。一起部署 

   - 高度的定制化，满足复杂的业务场景

   - Acegi 配置繁琐，集成springboot之后 开箱即用。

2. function

   - 认证
   - 自定义认证
   - 密码加密
   - remember me
   - 会话管理
   - SCRF
   - 跨域
   - 全局异常处理
   - 授权和授权模型
   - OAuth2 &JWT

3. 整体架构

   - <img src="https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250909-141325-8209378_9a96cd14-76c8-4b9d-8421-0c7f2fe07d4e.png" alt="img" style="zoom: 50%; float: left;" />
   - ![image-20250909141611279](https://leslieyedoc.oss-cn-shanghai.aliyuncs.com/img/20250909-141615-image-20250909141611279.png)
   - 参考[https://juejin.cn/post/7152281736494186527](https://juejin.cn/post/7152281736494186527)

   

