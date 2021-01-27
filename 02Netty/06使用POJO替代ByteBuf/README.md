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

