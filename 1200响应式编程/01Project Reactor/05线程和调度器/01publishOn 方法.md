# publishOn 方法

&emsp;&emsp;publishOn 以与任何其他操作符相同的方式应用于订阅者链的中间。它从上游获取信号并在下游重播它们，同时从关联的调度程序对 worker 执行回调。因此，它会影响后续运算符的执行位置（直到另一个 publishOn 链接进来），如下所示：

- 将执行上下文更改为调度程序选择的一个线程
- 根据规范，onNext 调用按顺序发生，因此这会占用一个线程
- 除非它们在特定的调度程序上工作，否则 publishOn 之后的运算符将继续在同一线程上执行

&emsp;&emsp;以下示例使用 publishOn 方法：

```java
Scheduler s = Schedulers.newParallel("parallel-scheduler", 4);  // 1 创建一个由四个线程实例支持的新调度程序。

final Flux<String> flux = Flux
    .range(1, 2)
    .map(i -> 10 + i)   // 2 第一个映射在 <5> 中的匿名线程上运行。
    .publishOn(s)   // 3 publishOn 在从 <1> 选取的 Thread 上切换整个序列。
    .map(i -> "value " + i);  // 4 第二个map在 <1> 的线程上运行。

new Thread(() -> flux.subscribe(System.out::println));  // 5 这个匿名线程是订阅发生的地方。打印发生在最新的执行上下文中，即来自 publishOn 的上下文。
```

