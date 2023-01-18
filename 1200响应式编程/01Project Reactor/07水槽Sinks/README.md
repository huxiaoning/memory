# 水槽Sinks

&emsp;&emsp;在 `Reactor` 中，`sink` 是一个允许以独立方式安全手动触发信号的类，创建一个类似于 `Publisher` 的结构，能够处理多个 `Subscriber`（`unicast()` 类型除外）。

&emsp;&emsp;在 3.5.0 之前，还有一套 Processor 实现已经被淘汰了。

