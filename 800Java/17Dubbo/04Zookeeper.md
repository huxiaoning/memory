# Zookeeper

- zookeeper分布式协调组件
  - 本质是一个软件
- Zookeeper常用功能
  - 发布订阅功能
  - 分布式/集群管理功能
- 使用java语言编写。



- [下载](https://zookeeper.apache.org/)
- 安装

```shel
$ docker pull zookeeper:3.8
$ docker run -it -d -p 127.0.0.1:2181:2181 --restart=always --name zookeeper zookeeper:3.8
```

