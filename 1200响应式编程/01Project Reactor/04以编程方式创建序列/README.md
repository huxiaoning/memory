# 以编程方式创建序列

&emsp;&emsp;在本节中，我们通过以编程方式定义其关联事件（onNext、onError 和 onComplete）来介绍 Flux 或 Mono 的创建。所有这些方法都有一个共同点，即它们公开了一个 API 来触发我们称为sink的事件。