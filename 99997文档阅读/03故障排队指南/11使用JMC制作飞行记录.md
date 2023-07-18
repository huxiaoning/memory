# 使用JMC制作飞行记录

&emsp;&emsp;使用 Java Mission Control (JMC) 可轻松管理飞行记录。首先，在最左边的JVM浏览器中找到您的服务器，如图2-1所示。

<center>图 2-1 Java 任务控制 - 查找服务器</center>

![](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/img/jmc-event.png)

&emsp;&emsp;默认情况下，将列出任何本地运行的 JVM。远程 JVM（以与运行 JMC 的用户相同的有效用户身份运行）必须设置为使用远程 JMX 代理，请参阅[使用 JMX 技术进行监控和管理](https://docs.oracle.com/javase/8/docs/technotes/guides/management/agent.html)。然后，单击新建 JVM 连接按钮，并输入网络详细信息。

&emsp;&emsp;在 JDK 8u40 发布之前，JVM 还必须使用以下标志启动：`-XX:+UnlockCommercialFeatures -XX:FlightRecorder`。

&emsp;&emsp;自JDK 8u40发布以来，Java Flight Recorder可在运行时启用。

&emsp;&emsp;以下是使用Java Mission Control制作飞行记录的三种方法。

&emsp;&emsp;1 检查正在运行的记录： 在 JVM 浏览器中展开节点，查看正在运行的记录。图 2-2 显示了正在运行的连续记录（带无穷大符号）和定时剖析记录。

<center>图 2-2 Java 任务控制 - 运行记录</center>

![](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/img/jmc-expand-node.png)

&emsp;&emsp;右键单击任何记录以转储、编辑或停止记录。停止剖析记录仍将生成记录文件，关闭剖析记录将丢弃记录。

&emsp;&emsp;2 转储连续记录： 右键单击 JVM 浏览器中的连续记录，然后选择转储到文件。在弹出的对话框中，选择转储所有可用数据或仅转储记录的最后部分，如图2-3所示。

<center>图 2-3 Java 任务控制 - 转储连续记录</center>

![](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/img/jmc-continue-record.png)