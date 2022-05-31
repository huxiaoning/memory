# Dubbo

一个分布式、高性能、透明化的`RPC`服务框架，提供自动注册、自动发现等高效服务器治理方案。



![](https://dubbo.apache.org/imgs/architecture.png)

### Dubbo架构

- Provider
  - 提供者，服务发布方
- Consumer
  - 消费者，调用服务方
- Container
  - Dubbo容器，依赖于Spring容器
- Registry
  - 注册中心，当Container启动时把所有可以提供的服务列表向Registry注册。
  - 作用：告诉Consumer提供了什么服务和服务方在哪里。

- Monitor
  - Consumer和Provider每隔1分钟向Monitor发送统计信息，统计信息包含访问次数、频率等。
  - 它的作用是发现某个服务的压力太大了，我们可以把这个服务拆分或再增加部署节点。

 

##### Consumer如何知道Provider挂了：

`zookeeper`提供了“心跳检测”功能，它会定时向各个服务提供者发送一个请求（实际上建立的是一个 socket 长连接），如果长期没有响应，服务中心就认为该服务提供者已经“挂了”，并将其剔除。

服务消费方会监听`zookeeper`相应的路径，一旦路径上的数据有任何的变化，`zookeeper`就会将新的服务列表发送给消费方，消费方在获取到数据后，刷新本地缓存的列表。

类似观察都模式。

##### 远程调用时使用的对象为代理对象(JDK动态代理)





