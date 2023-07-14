# Java Mission Control

&emsp;&emsp;Java Mission Control (JMC)是针对HotSpot JVM的全新JDK剖析和诊断工具平台。它是用于基本监控、管理和生产时间剖析与诊断的高性能工具套件。Java Mission Control将性能开销降至最低，而性能开销通常是剖析工具的一个问题。该工具是JVM内置的商业功能，可在运行时使用。

&emsp;&emsp;Java Flight Recorder (JFR) 是一项商业功能。您可以在开发人员的台式机/笔记本电脑上免费使用，也可以在测试、开发和生产环境中出于评估目的使用。但是，要在生产服务器上启用 JFR，您需要获得商业许可。在 JDK 上出于其他目的使用 JMC UI 不需要商业许可。

&emsp;&emsp;Java Mission Control (JMC)由Java Management Console (JMX)、Java Flight Recorder (JFR)和其他几个可从该工具下载的插件组成。JMX是一个用于监控和管理Java应用程序的工具，JFR是一个剖析工具。Java Mission Control还可作为Eclipse IDE的一组插件提供。

&emsp;&emsp;以下主题介绍如何使用 Java Mission Control 进行故障排除。

- 使用Java Mission Control排除故障

##### 使用Java Mission Control排除故障

&emsp;&emsp;Java Mission Control允许您执行以下故障排除活动：

- Java管理控制台（JMX）连接到运行中的JVM，实时收集并显示关键特征。
- 触发用户为JVM提供的自定义操作和规则，需要JDK 8。
- JMC工具中的WLS、DTrace、JOverflow等实验性插件提供了故障诊断功能。
  - DTrace插件是一种扩展的DScript语言，用于生成自描述事件。它提供类似于Flight Recorder的可视化功能。
  - JOverflow 是另一个用于分析堆浪费（空/稀少集合）的插件工具，它使用 hprof dump。建议使用JDK 8版本以优化JOverflow插件的使用。
- Java Mission Control 中的 Java Flight Recordings (JFR) 可用于分析事件。预配置的选项卡允许轻松深入到共同感兴趣的各个领域，如代码、内存和GC、线程和IO。一般事件选项卡和操作事件选项卡允许进一步钻取并快速定位具有特定属性的事件集。事件 "选项卡通常有复选框，仅显示操作集中的事件。
  - JFR作为JMC客户端的插件，以逻辑分组的表格、图表和刻度盘显示诊断信息。它使您能够选择必要的时间范围和详细程度来关注问题。有关 JFR 的更多详情，请参阅 Java Flight Recorder。
- Java Mission Control插件使用Java管理扩展（JMX）代理连接到JVM。JMX是用于管理和监控资源（如应用程序、设备、服务和Java虚拟机）的标准API。有关JMX的详细信息，请参阅Java Management Extensions。

&emsp;&emsp;有关JMC的详细信息，请参见[JMC文档](https://docs.oracle.com/en/java/java-components/index.html)。