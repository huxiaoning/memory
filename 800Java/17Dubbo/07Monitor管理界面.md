# Monitor管理界面

- 本质就是一个web项目，是dubbo提供的一个war包。
- 获取注册中心内Provider注册的信息，用页面呈现出来。





现在打出来的已经是jar包了。

`https://dubbo.apache.org/zh/docs/v2.7/admin/ops/introduction/`



这个主要拿来看一下服务有没有发布成功。



```shell
$ git clone https://github.com/apache/dubbo-admin.git
$ cd dubbo-admin
$ mvn clean package
$ cd dubbo-admin-distribution/target
$ java -jar dubbo-admin-0.1.jar
```



配置

- `dubbo-admin-server/src/main/resources/application.properties`

```properties
admin.config-center=zookeeper://127.0.0.1:2181
admin.registry.address=zookeeper://127.0.0.1:2181
admin.metadata-report.address=zookeeper://127.0.0.1:2181
admin.root.user.name=root
admin.root.user.password=root
```







