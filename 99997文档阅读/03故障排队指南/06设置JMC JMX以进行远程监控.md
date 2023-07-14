# 设置JMC JMX以进行远程监控

&emsp;&emsp;JMX可用于使用Mission Control或Visual VM等工具远程连接Java应用程序。除非您可以在运行应用程序的同一台机器上运行这些工具，否则设置这些工具有助于日后监控应用程序、发送诊断命令、管理飞行记录等。启用JMX没有性能开销。

&emsp;&emsp;请参阅[有关如何使用 JMX 技术监控 JVM 的说明](https://docs.oracle.com/javase/8/docs/technotes/guides/management/agent.html)。

&emsp;&emsp;在 Java 应用程序启动后启用 JMX 的另一种方法是使用诊断命令 `ManagementAgent.start`。运行 `jcmd <pid> help ManagementAgent.start`，查看可与该命令一起发送的标志列表。

&emsp;&emsp;有关`jcmd`的更多详情，请参阅[jcmd实用程序](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr006.html#BABEHABG)。