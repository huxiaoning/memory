# 处理错误

&emsp;&emsp;要快速查看可用于错误处理的运算符，请参阅[相关的运算符决策树](https://projectreactor.io/docs/core/release/reference/#which.errors)。

&emsp;&emsp;在 Reactive Streams 中，错误是终端事件。一旦发生错误，它就会停止序列并沿着运算符链向下传播到最后一步，即您定义的订阅者及其 `onError` 方法。

&emsp;&emsp;此类错误仍应在应用程序级别处理。例如，您可能会在 `UI` 中显示错误通知或在 REST 端点中发送有意义的错误负载。出于这个原因，应该始终定义订阅者的 `onError` 方法。

&emsp;&emsp;如果未定义，`onError` 将抛出 `UnsupportedOperationException`。您可以使用 `Exceptions.isErrorCallbackNotImplemented` 方法进一步检测和分类它。

&emsp;&emsp;Reactor 还提供了处理链中间错误的替代方法，如错误处理运算符。以下示例显示了如何执行此操作：

```java
Flux.just(1, 2, 0)
    .map(i -> "100 / " + i + " = " + (100 / i)) //this triggers an error with 0
    .onErrorReturn("Divided by zero :("); // error handling example
```

> 在学习错误处理运算符之前，您必须牢记反应序列中的任何错误都是终止事件。即使使用错误处理运算符，它也不会让原始序列继续。相反，它将 `onError` 信号转换为新序列（回退序列）的开始。换句话说，它取代了它上游的终止序列。

&emsp;&emsp;现在我们可以逐一考虑每种错误处理方式。相关时，我们将与命令式编程的尝试模式进行比较。

