# 02`Spring-Session`

```xml
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session</artifactId>
    <version>1.3.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-jdbc</artifactId>
    <version>2.7.0</version>
</dependency>
```

spring-session 技术是spring 提供的用于处理集群会话共享的解决方案。

spring-session 技术是将用户session数据保存到三方存储容器中，如：mysql，redis等。

Spring-session 技术是解决**同域名**下的多**服务器集群session 共享**问题的。**不能解决跨域session 共享**问题。



要用spring-session来实现sso单点登录，需要在所有系统前端部署配置Nginx,相当麻烦了，推荐后续的`JWT`来实现单点登录。

