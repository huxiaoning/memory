# 用它的 Disposable 取消一个 subscribe()

&emsp;&emsp;所有这些基于 lambda 的 subscribe() 变体都有一个 Disposable 返回类型。在这种情况下，Disposable 接口表示可以通过调用其 dispose() 方法取消订阅。

&emsp;&emsp;对于 Flux 或 Mono，取消是源应停止生成元素的信号。但是，它不能保证是立即的：某些源可能会非常快地生成元素，甚至在收到取消指令之前就可以完成。

&emsp;&emsp;Disposables 类中提供了一些围绕 Disposable 的实用工具。其中，Disposables.swap() 创建了一个 Disposable 包装器，可让您自动取消和替换具体的 Disposable。这可能很有用，例如，在您想要取消请求并在用户单击按钮时将其替换为新请求的 UI 场景中。处理包装器本身将其关闭。这样做会处理当前的具体值和所有未来尝试的替换。

&emsp;&emsp;另一个有趣的实用工具是 Disposables.composite(…)。该组合让您可以收集多个 Disposable——例如，与服务调用关联的多个正在运行的请求——并在稍后一次性处理所有这些请求。一旦调用了组合的 dispose() 方法，任何添加另一个 Disposable 的尝试都会立即释放它。

