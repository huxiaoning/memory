# subscribeOn 方法

&emsp;&emsp;subscribeOn在构建反向链时应用于订阅过程。因此，无论您将 subscribeOn 放在链中的什么位置，它总是会影响源发射的上下文。但是，这不会影响后续调用publishOn的行为 — 它们仍然为后面的链部分切换执行上下文。

- 更改整个操作符链订阅的线程
- 从调度程序中选择一个线程

&emsp;&emsp;实际上只考虑链中最早的subscribeOn调用。

以下示例使用 subscribeOn 方法：

```java
Scheduler s = Schedulers.newParallel("parallel-scheduler", 4);  // 1 创建一个由四个线程支持的新调度程序。
final Flux<String> flux = Flux
    .range(1, 2)
    .map(i -> 10 + i)  // 2 第一个map在这四个线程之一上运行…
    .subscribeOn(s)   // 3 …因为 subscribeOn 从订阅时间 (<5>) 开始切换整个序列。
    .map(i -> "value " + i);  // 4 第二个map也在同一线程上运行。

new Thread(() -> flux.subscribe(System.out::println)); // 5 这个匿名线程是最初发生订阅的线程，但 subscribeOn 立即将其转移到四个调度程序线程之一。 
```

&emsp;&emsp;注意：这里的两个map和subscribe中的回调，都使用第1行的调度线程。

