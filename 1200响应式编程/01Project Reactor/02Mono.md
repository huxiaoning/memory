# Mono 一个包含0~1个元素的异步序列

下图显示了 Mono 如何转换项目：

![Mono](https://projectreactor.io/docs/core/release/reference/images/mono.svg)



- This is the timeline of the Mono Time flows from left to right
  - 这是Mono Time从左到右流动的时间轴
- This is the optional item emitted by the Mono
  - 这是 Mono 发出的可选项目
- This vertical line indicates that the Mono has completed successfully.
  - 这条垂直线表示 Mono 已成功完成
- These dotted lines and this box indicate that a transformation is being applied to the Mono
  - 这些虚线和此框表示正在对 Mono应用转换
- The text inside the box shows the nature of the transformation
  - 框内的文本显示了转换的性质
- This Mono is the result of the transformation
  - 这个Mono是转换后的结果
- If for some reason the Mono terminates abnormally, with an error,the vertical line is replaced by an X
  - 如果由于某种原因 Mono 异常终止，出现错误，垂直线将被替换为 X(叉号)



Mono<T> 是一个特殊的 Publisher<T>，它最多可以通过onNext发出一个元素， 然后通过onComplete以一个信号（成功，有或没有值）终止，或者以onError只发出一个信号（失败）。



大多数`Mono`实现预计会`onComplete`在 `Subscriber`调用`onNext`。`Mono.never()`是一个异常值：它不发出任何信号，这在技术上并不被禁止，尽管在测试之外并不是很有用。另一方面，明确禁止`onNext`和`onError`的组合。



Mono提供的操作符是Flux操作符的子集(有些操作Flux支持，但Mono没有)，Mono还提供一些可以切换到Flux的操作符（特别是那些将 Mono 与另一个 Publisher 结合在一起的）。



请注意，您可以使用 Mono 来表示只有完成概念的无值异步进程（类似于 Runnable）。要创建一个，您可以使用空的 Mono<Void>。
