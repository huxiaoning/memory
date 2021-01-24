## `TimeDecoder`类

```java
package org.aidan.client;

import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandlerContext;
import io.netty.handler.codec.ReplayingDecoder;
import org.aidan.pojo.UnixTime;

import java.util.List;

/**
 * @author aidan
 */
public class TimeDecoder extends ReplayingDecoder<Void> {
    @Override
    protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) {
        out.add(new UnixTime(in.readInt()));
    }
}
```

