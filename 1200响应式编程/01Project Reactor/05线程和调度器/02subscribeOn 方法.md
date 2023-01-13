# subscribeOn 方法

&emsp;&emsp;subscribeOn在构建反向链时应用于订阅过程。因此，无论您将 subscribeOn 放在链中的什么位置，它总是会影响源发射的上下文。但是，这不会影响后续调用publishOn的行为 — 它们仍然为后面的链部分切换执行上下文。

- 更改整个操作符链订阅的线程
- 从调度程序中选择一个线程

&emsp;&emsp;实际上只考虑链中最早的subscribeOn调用。

以下示例使用 subscribeOn 方法：

```java
Scheduler s = Schedulers.newParallel("parallel-scheduler", 4); 

final Flux<String> flux = Flux
    .range(1, 2)
    .map(i -> 10 + i)  
    .subscribeOn(s)  
    .map(i -> "value " + i);  

new Thread(() -> flux.subscribe(System.out::println));  
```

