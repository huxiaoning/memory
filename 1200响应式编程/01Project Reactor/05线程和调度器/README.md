# 线程和调度器

&emsp;&emsp;Reactor 和 RxJava 一样，可以被认为是并发不可知的。也就是说，它不强制执行并发模型。相反，它让开发人员自己来控制。但是，这并不妨碍该库帮助您处理并发。

&emsp;&emsp;获得一个Flux或者一个Mono并不一定意味着它运行在一个专用的Thread中。相反，大多数运算符继续在前一个运算符执行的线程中工作。除非指定，否则最顶层的运算符（源）本身在调用 subscribe() 的线程上运行。以下示例在新线程中运行 Mono：

```java
public static void main(String[] args) throws InterruptedException {
  final Mono<String> mono = Mono.just("hello ");  // 1 Mono<String> 在主线程中组装。
  Thread t = new Thread(() -> mono
      .map(msg -> msg + "thread ")
      .subscribe(v ->  // 2 但是，它是在线程 Thread-0 中订阅的。
          System.out.println(v + Thread.currentThread().getName()) // 3 因此，map 和 onNext 回调实际上都在 Thread-0 中运行
      )
  );
  t.start();
  t.join();
}
```

&emsp;&emsp;前面的代码产生以下输出：

```bash
hello thread Thread-0
```

&emsp;&emsp;在 Reactor 中，执行模型和执行发生的位置由所使用的调度程序决定。Scheduler 具有类似于 ExecutorService 的调度职责，但具有专用的抽象使其可以做更多的事情，特别是充当时钟并支持更广泛的实现（用于测试的虚拟时间、蹦床或立即调度等）。

&emsp;&emsp;Schedulers 类具有静态方法，可以访问以下执行上下文：

- 无执行上下文（Schedulers.immediate()）：在处理时，提交的 Runnable 将被直接执行，有效地在当前 Thread 上运行它们（可以看作是“空对象”或无操作调度程序）。
- 单个可重用线程 (Schedulers.single())。请注意，此方法对所有调用者重复使用相同的线程，直到调度程序被释放。如果您想要每次调用专用线程，请对每次调用使用 Schedulers.newSingle()。
- 无界弹性线程池 (Schedulers.elastic())。随着 Schedulers.boundedElastic() 的引入，这个不再是首选，因为它有隐藏背压问题并导致过多线程的倾向（见下文）。
- 有界弹性线程池 (Schedulers.boundedElastic())。与其前身 elastic() 一样，它会根据需要创建新的工作池并重用空闲的工作池。闲置时间过长（默认为 60 秒）的工作池也会被处理掉。与其前身 elastic() 不同，它对可创建的支持线程数有上限（默认为 CPU 核心数 x 10）。达到上限后提交的最多 100 000 个任务将被排入队列，并在线程可用时重新安排（当使用延迟调度时，延迟在线程可用时开始）。这是 I/O 阻塞工作的更好选择。 Schedulers.boundedElastic() 是一种方便的方法，可以为阻塞进程提供自己的线程，这样它就不会占用其他资源。请参阅如何包装同步的阻塞调用？，但不会对系统施加太大的新线程压力。
- 为并行工作调整的固定工作人员池 (Schedulers.parallel())。它会创建与您拥有的 CPU 内核一样多的工作人员。

&emsp;&emsp;此外，您可以使用 Schedulers.fromExecutorService(ExecutorService) 从任何预先存在的 ExecutorService 创建一个 Scheduler。 （您也可以从 Executor 创建一个，尽管不鼓励这样做。）

&emsp;&emsp;您还可以使用 newXXX 方法创建各种调度程序类型的新实例。例如，Schedulers.newParallel(yourScheduleName) 创建一个名为 yourScheduleName 的新并行调度程序。

&emsp;&emsp;虽然 boundedElastic 旨在帮助解决无法避免的遗留阻塞代码，但 single 和 parallel 却不能。因此，在默认的single和parallel调度程序中使用 Reactor 阻塞 API（block()、blockFirst()、blockLast()（以及遍历 toIterable() 或 toStream()）会导致抛出 IllegalStateException .

&emsp;&emsp;通过创建实现 NonBlocking 标记接口的 Thread 实例，自定义调度程序也可以标记为“仅非阻塞”。

&emsp;&emsp;一些操作员默认使用 Schedulers 中的特定调度程序（并且通常会让您选择提供不同的调度程序）。例如，调用 Flux.interval(Duration.ofMillis(300)) 工厂方法会生成每 300 毫秒计时一次的 Flux<Long>。默认情况下，这是由 Schedulers.parallel() 启用的。以下行将 Scheduler 更改为类似于 Schedulers.single() 的新实例：

```java
Flux.interval(Duration.ofMillis(300), Schedulers.newSingle("test"))
```

&emsp;&emsp;Reactor 提供了两种在反应链中切换执行上下文（或调度程序）的方法：publishOn 和 subscribeOn。两者都采用调度程序并让您将执行上下文切换到该调度程序。但是 publishOn 在链中的位置很重要，而 subscribeOn 的位置则无关紧要。要了解这种差异，您首先必须记住，在您订阅之前什么都不会发生。

&emsp;&emsp;在 Reactor 中，当您链接运算符时，您可以根据需要将尽可能多的 Flux 和 Mono 实现包装在彼此中。订阅后，将创建一个订阅者对象链，向后（沿着链向上）到第一个发布者。这实际上对您隐藏了。您所能看到的只是 Flux（或 Mono）和 Subscription 的外层，但这些中间操作符特定的订阅者才是真正工作发生的地方。

&emsp;&emsp;有了这些知识，我们可以仔细看看 publishOn 和 subscribeOn 运算符：

