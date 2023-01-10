# Flux 一个包含0~N个元素的异步序列

下图显示了 Flux 如何转换项目：

![Flux](https://projectreactor.io/docs/core/release/reference/images/flux.svg)

- This is the timeline of the Flux Time flows from left to right
  - 这是 Flux Time 从左到右流动的时间轴
- These are the items emitted by the Flux
  - 这些是 Flux 发出的项目
- This vertical line indicates that the Flux has completed successfully
  - 这条垂直线表示 Flux 已成功完成
- These dotted lines and this box indicate that a transformation is being applied to the Flux
  - 这些虚线和此框表示正在对 Flux 应用转换
- The text inside the box shows the nature of the transformation
  - 框内的文本显示了转换的性质
- This Flux is the result of the transformation
  - 这个 Flux 是转换的结果
- If for some reason the Flux terminates abnormally,with an error, the vertical line is replaced by an X
  - 如果由于某种原因 Flux 异常终止，并出现错误，垂直线将被替换为 X(叉号)



​	`Flux<T>`是一个标准`Publisher<T>`，表示 0 到 N 个发射项的异步序列，可选地由完成信号或错误终止。与 Reactive Streams 规范一样，这三种类型的信号转换为对下游订阅者的`onNext`、`onComplete`和`onError`方法的调用。



​	具有这种大范围的可能信号，`Flux`是通用反应型。请注意，所有事件，甚至是终止事件，都是可选的：没有`onNext`事件，只有一个 `onComplete`事件代表一个*空*的有限序列，但是删除了`onComplete`，你就有了一个*无限*的空序列（不是特别有用，除了围绕取消的测试）。同样，无限序列不一定是空的。例如，`Flux.interval(Duration)` 生成一个`Flux<Long>`无限大的并从时钟发出规律的滴答声。