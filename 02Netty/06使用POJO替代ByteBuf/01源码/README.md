## 使用POJO替代ByteBuf源码

### 项目结构

![image-20210124083902038](https://raw.githubusercontent.com/huxiaoning/img/master/20210124083903.png)





源码中的编解码器有点高级，不太容易懂，这里放一下原始一点的编解码器。

### `TimeEncoder`

```java
package org.aidan.server;

import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelOutboundHandlerAdapter;
import io.netty.channel.ChannelPromise;
import org.aidan.pojo.UnixTime;

/**
 * @author aidan
 */
public class TimeEncoder extends ChannelOutboundHandlerAdapter {
    @Override
    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) {
        UnixTime m = (UnixTime) msg;
        ByteBuf encoded = ctx.alloc().buffer(4);
        encoded.writeInt((int) m.value());
        ctx.write(encoded, promise);
    }
}
```

### `TimeDecoder`

```java
package org.aidan.client;

import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandlerContext;
import io.netty.handler.codec.ByteToMessageDecoder;
import org.aidan.pojo.UnixTime;

import java.util.List;

/**
 * @author aidan
 */
public class TimeDecoder extends ByteToMessageDecoder {
    @Override
    protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) {
        if (in.readableBytes() < 4) {
            return;
        }
        out.add(new UnixTime(in.readInt()));
    }
}
```

