## 关闭`Netty`应用程序

关闭`Netty`应用程序非常简单，关闭你所建的所有`EventLoopGroup`即可。

我们调用`EventLoopGroup`对象的`shutdownGracefully()`方法就可以关闭`EventLoopGroup`对象。

`EventLoopGroup`对象的`shutdownGracefully()`方法返回一个`Future`实例。

这个实例的`get()`方法将等待两件事情的完成：

1. `EventLoopGroup`完全关闭。
2. 所属`EventLoopGroup`的全部通道`Channel`被全部关闭。



示例代码：

```java
      	EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            // ......
        } finally {
            // 关闭服务器
            Future<?> workFuture = workerGroup.shutdownGracefully();
            Future<?> bossFuture = bossGroup.shutdownGracefully();
            workFuture.get();
            bossFuture.get();
        }
```

```java
				EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            // ......
        } finally {
          	// 关闭客户端
            Future<?> future = workerGroup.shutdownGracefully();
            future.get();
        }
```

