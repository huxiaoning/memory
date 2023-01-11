# Lambdas 的替代品：BaseSubscriber

&emsp;&emsp;还有一个更通用的附加订阅方法，它采用一个成熟的订阅者，而不是从 lambda 中组合一个。为了帮助编写这样的订阅者，我们提供了一个名为 BaseSubscriber 的可扩展类。

&emsp;&emsp;BaseSubscriber 的实例（或其子类）是一次性的，这意味着如果 BaseSubscriber 订阅了第二个发布者，它会取消对第一个发布者的订阅。这是因为两次使用一个实例会违反 Reactive Streams 的规则，即不得并行调用订阅者的 onNext 方法。因此，匿名实现只有在对 Publisher#subscribe(Subscriber) 的调用中直接声明时才适用。