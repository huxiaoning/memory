# 同步的generate

&emsp;&emsp;以编程方式创建 Flux 的最简单形式是通过 generate 方法，该方法采用生成器函数。

&emsp;&emsp;这是针对同步和逐一发射的，这意味着接收器是 SynchronousSink 并且其 next() 方法每次回调调用最多只能调用一次。然后您可以另外调用 error(Throwable) 或 complete()，但这是可选的。

&emsp;&emsp;最有用的变体可能是让您保持状态的变体，您可以在您的接收器使用中参考该状态来决定接下来要发出什么。生成器函数然后变为 `BiFunction<S, SynchronousSink<T>, S>`，其中 `<S>` 是状态对象的类型。您必须为初始状态提供 `Supplier<S>` ，并且您的生成器函数现在在每一轮都返回一个新状态。

&emsp;&emsp;例如，您可以使用 int 作为状态：

&emsp;&emsp;示例 11. 基于状态的生成示例

```java
Flux<String> flux = Flux.generate(
    () -> 0,  // 我们提供初始状态值 0。
    (state, sink) -> {
      sink.next("3 x " + state + " = " + 3*state);  // 我们使用状态来选择要发出的内容（3 的乘法表中的一行）。
      if (state == 10) sink.complete();  // 我们还用它来选择何时停止。
      return state + 1;  // 我们返回在下一次调用中使用的新状态（除非序列在此调用中终止）。
    });
```

&emsp;&emsp;上述代码生成表3，顺序如下：

```bash
3 x 0 = 0
3 x 1 = 3
3 x 2 = 6
3 x 3 = 9
3 x 4 = 12
3 x 5 = 15
3 x 6 = 18
3 x 7 = 21
3 x 8 = 24
3 x 9 = 27
3 x 10 = 30
```

&emsp;&emsp;您还可以使用可变的 <S>。例如，可以使用单个 AtomicLong 作为状态重写上面的示例，并在每一轮对其进行修改：

&emsp;&emsp;示例 12. 可变状态变体

```java
Flux<String> flux = Flux.generate(
    AtomicLong::new,  // 这次，我们生成一个可变对象作为状态
    (state, sink) -> {
      long i = state.getAndIncrement();  // 我们在这里改变状态。
      sink.next("3 x " + i + " = " + 3*i);
      if (i == 10) sink.complete();
      return state;  // 我们返回与新状态相同的实例。
    });
```

&emsp;&emsp;如果您的状态对象需要清理一些资源，请使用 generate(Supplier<S>, BiFunction, Consumer<S>) 变体来清理最后一个状态实例。

&emsp;&emsp;以下示例使用包含 Consumer 的 generate 方法：

```java
Flux<String> flux = Flux.generate(
    AtomicLong::new,
      (state, sink) -> {  // 同样，我们生成一个可变对象作为状态。
      long i = state.getAndIncrement();  // 我们在这里改变状态。
      sink.next("3 x " + i + " = " + 3*i);
      if (i == 10) sink.complete();
      return state;  // 我们返回与新状态相同的实例。
    }, (state) -> System.out.println("state: " + state));  // 我们将最后一个状态值 (11) 视为此 Consumer lambda 的输出。
```

&emsp;&emsp;在状态包含数据库连接或其他需要在流程结束时处理的资源的情况下，消费者 lambda 可以关闭连接或以其他方式处理应在流程结束时完成的任何任务。

