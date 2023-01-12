# 异步单线程push

&emsp;&emsp;push 是 generate 和 create 之间的中间地带，适用于处理来自单个生产者的事件。它在某种意义上类似于 create，它也可以是异步的，并且可以使用 create 支持的任何溢出策略来管理背压。但是，一次只有一个生产线程可以调用 next、complete 或 error。

```java
Flux<String> bridge = Flux.push(sink -> {
    myEventProcessor.register(
      new SingleThreadEventListener<String>() {  // 1 连接到 SingleThreadEventListener API。

        public void onDataChunk(List<String> chunk) {
          for(String s : chunk) {
            sink.next(s);  // 2 使用单个侦听器线程中的 next 将事件推送到接收器。
          }
        }

        public void processComplete() {
            sink.complete();  // 3 从同一侦听器线程生成的 complete 事件。
        }

        public void processError(Throwable e) {
            sink.error(e);  // 4 error事件也从同一个侦听器线程生成。
        }
    });
});
```

&emsp;&emsp;完整示例代码：

```java
import reactor.core.publisher.Flux;
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;
public class FluxPush {
    public static void main(String[] args) {
        MyEventProcessor myEventProcessor = new ScheduledSingleListenerEventProcessor();
        Flux<String> bridge = Flux.push(sink -> {
            myEventProcessor.register(new SingleThreadEventListener<String>() {
                public void onDataChunk(List<String> chunk) {
                    for (String s : chunk) {
                        sink.next(s);
                    }
                }
                public void processComplete() {
                    sink.complete();
                }
                public void processError(Throwable e) {
                    sink.error(e);
                }
            });
        });
    }
}
interface SingleThreadEventListener<T> {
    void onDataChunk(List<T> chunk);
    void processComplete();
    void processError(Throwable e);
}
interface MyEventProcessor<T> {
    void register(SingleThreadEventListener<String> eventListener);
    void fireEvents(String... values);
    void processComplete();
    void shutdown();
}
class ScheduledSingleListenerEventProcessor implements MyEventProcessor {
    private SingleThreadEventListener<String> eventListener;
    private ScheduledExecutorService executor = Executors.newSingleThreadScheduledExecutor();
    @Override
    public void register(SingleThreadEventListener eventListener) {
        this.eventListener = eventListener;
    }
    @Override
    public void fireEvents(String... values) {
        //每个半秒发送一个事件
        executor.schedule(() -> eventListener.onDataChunk(Arrays.asList(values)), 500, TimeUnit.MILLISECONDS);
    }
    @Override
    public void processComplete() {
        executor.schedule(() -> eventListener.processComplete(), 500, TimeUnit.MILLISECONDS);
    }
    @Override
    public void shutdown() {
        this.executor.shutdownNow();
    }
}
```

---

### 混合推/拉模型

&emsp;&emsp;大多数 Reactor 操作符，如 create，都遵循混合推/拉模型。我们的意思是，尽管大部分处理是异步的（建议采用推送方法），但它有一个小的拉动组件：请求。

&emsp;&emsp;消费者从源中提取数据，因为它在第一次请求之前不会发出任何东西。源在数据可用时将数据推送给消费者，但在其请求数量的范围内。

&emsp;&emsp;请注意，push() 和 create() 都允许设置一个 onRequest 消费者，以便管理请求量并确保只有在有未决请求时才通过接收器推送数据。

```java
Flux<String> bridge = Flux.create(sink -> {
    myMessageProcessor.register(
      new MyMessageListener<String>() {

        public void onMessage(List<String> messages) {
          for(String s : messages) {
            sink.next(s);  // 3 稍后异步到达的其余消息也将被传递。
          }
        }
    });
    sink.onRequest(n -> {
        List<String> messages = myMessageProcessor.getHistory(n);  // 1 发出请求时轮询消息。
        for(String s : messages) {
           sink.next(s);  // 2 如果消息立即可用，将它们推送到接收器。
        }
    });
});
```

---

### 在 push() 或 create() 之后清理

&emsp;&emsp;两个回调，onDispose 和 onCancel，在取消或终止时执行任何清理。onDispose 可用于在 Flux 完成、出错或取消时执行清理。onCancel 可用于在使用 onDispose 进行清理之前执行特定于取消的任何操作。

```java
Flux<String> bridge = Flux.create(sink -> {
    sink.onRequest(n -> channel.poll(n))
        .onCancel(() -> channel.cancel())  // 1 onCancel 首先被调用，仅用于取消信号。
        .onDispose(() -> channel.close())  // 2 onDispose 为完成、错误或取消信号调用。
    });
```

