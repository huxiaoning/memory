## 使用`POJO`替换`ByteBuf`

到目前为止，我们回顾的所有示例都使用`ByteBuf`作为协议消息的主要数据结构。

在本节中，我们将改进时间协议客户机和服务器示例，以使用`POJO`而不是`ByteBuf`。



在`ChannelHandlers`中使用`POJO`的好处是显而易见的；

通过将从`ByteBuf`中提取信息的代码从处理程序中分离出来，处理程序变得更易于维护和重用。

在时间客户机和服务器示例中，我们只读取一个32位整数，直接使用`ByteBuf`不是主要问题。

但是，在实现实际协议时，你会发现有必要进行分离。

首先，让我们定义一个名为`UnixTime`的新类型:

```java
package io.netty.example.time;

import java.util.Date;

public class UnixTime {

    private final long value;
    
    public UnixTime() {
        this(System.currentTimeMillis() / 1000L + 2208988800L);
    }
    
    public UnixTime(long value) {
        this.value = value;
    }
        
    public long value() {
        return value;
    }
        
    @Override
    public String toString() {
        return new Date((value() - 2208988800L) * 1000L).toString();
    }
}
```

我们现在可以修改时间解码器`TimeDecoder`，(解码)生成`UnixTime`而不是`ByteBuf`。

```java
@Override
protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) {
    if (in.readableBytes() < 4) {
        return;
    }

    out.add(new UnixTime(in.readUnsignedInt()));
}
```

有了更新后的解码器`TimeDecoder`,`TimeClientHandler`就不在需要使用`ByteBuf`了：

```java
@Override
public void channelRead(ChannelHandlerContext ctx, Object msg) {
    UnixTime m = (UnixTime) msg;
    System.out.println(m);
    ctx.close();
}
```

更简单更优雅，对吧？同样的技术也可以应用于服务器端。这次让我们先更新`TimeServerHandler`：

```java
@Override
public void channelActive(ChannelHandlerContext ctx) {
    ChannelFuture f = ctx.writeAndFlush(new UnixTime());
    f.addListener(ChannelFutureListener.CLOSE);
}
```

现在，唯一缺少的部分是编码器`TimeEncoder`，它是`ChannelOutboundHandler`的一个实现，它将`UnixTime`转换(编码)回ByteBuf。它比编写解码器简单得多，因为在编码消息时不需要处理数据包碎片和组装。

```java
package io.netty.example.time;

public class TimeEncoder extends ChannelOutboundHandlerAdapter {
    @Override
    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) {
        UnixTime m = (UnixTime) msg;
        ByteBuf encoded = ctx.alloc().buffer(4);
        encoded.writeInt((int)m.value());
        ctx.write(encoded, promise); // (1)
    }
}
```

1. 这一行有两点需要提一下：
   - 首先，我们按原样传递原始`ChannelPromise`，以便`Netty`在编码数据实际写入到网络线路时将其标记为成功或失败。
   - 其次，我们不调用`ctx.flush()`方法。`TimeEncoder`的父类`ChannelOutboundHandlerAdapter`有一个单独的处理程序方法`void flush（ChannelHandlerContext ctx）`，用于重写`flush（）`操作。

为了进一步简化，你也可以使用`MessageToByteEncoder`：

```java
public class TimeEncoder extends MessageToByteEncoder<UnixTime> {
    @Override
    protected void encode(ChannelHandlerContext ctx, UnixTime msg, ByteBuf out) {
        out.writeInt((int)msg.value());
    }
}
```

