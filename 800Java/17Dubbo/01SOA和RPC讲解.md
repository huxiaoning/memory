# SOA和RPC讲解

### SOA

`Service Oriented Ambiguity` 面向服务架构

- 有一个专门提供服务的单元
- 其他所有单元都调用这个服务。
- 举例：
  - Maven多模块项目中
  - xxx-parent为父模块、xxx-order与xxx-user为两个子模块，也是两个web项目，这两个模块依赖的数据库访问服务，抽取为一个模块xxx-db(把mapper都放这里)，俩都调用这个数据库访问服务。实现数据访问控制和代码复用。

##### SOA定位

- 如何**设计项目**，让开发时更有效率。
- SOA是一种思想。



##### 实现SOA架构时，几种常用服务

- Dubbo
- WebService
- Dubbox
- 服务方就是web项目，调用web项目的控制器



### RPC

`Remote Procedure Call Protocol` 远程过程调用协议

客户端通过网络调用远程服务器，不知道远程服务器具体实现，只知道远程服务器提供了什么功能。

