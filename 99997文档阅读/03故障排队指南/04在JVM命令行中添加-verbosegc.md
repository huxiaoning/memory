# 在JVM命令行中添加-verbosegc

&emsp;&emsp;标志 `-verbosegc` 记录有关 Java 垃圾收集器的基本信息。该日志可帮助您找到以下内容：

- 垃圾回收运行时间长吗？
- 可用内存是否会随着时间的推移而减少？

&emsp;&emsp;当应用程序抛出`OutOFMemoryError`或应用程序遇到性能问题时，垃圾收集器日志有助于诊断问题；因此，默认情况下打开`-verbosegc`标志有助于排除故障。

> 注意: 使用日志轮转可以避免应用程序重启时删除之前的日志。自 JDK7 起，标志 `UseGClogFileRotation` 和 `NumberOfGCLogFiles` 可用于设置日志轮换。有关这些标志的说明，请参阅 [Java HotSpot VM 的调试选项](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)。