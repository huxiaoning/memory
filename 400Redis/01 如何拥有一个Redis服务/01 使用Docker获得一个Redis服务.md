# 使用`Docker`获得一个`Redis`服务



```powershell
# docker redis 文档地址 https://hub.docker.com/_/redis
$ docker run --rm -it -d --name myredis -p 6379:6379 redis:6
```

[可视化的`redis`客户端](https://github.com/caoxinyu/RedisClient/tree/windows/release)

```
https://github.com/caoxinyu/RedisClient/tree/windows/release
```

`Java`客户端

```xml
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.6.1</version>
        </dependency>
```

