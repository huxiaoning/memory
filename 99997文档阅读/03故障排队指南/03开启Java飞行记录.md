# 开启Java飞行记录

&emsp;&emsp;Java Flight Recorder (JFR) 是一项商业功能。您可以在开发人员的台式机/笔记本电脑上免费使用，也可以在测试、开发和生产环境中出于评估目的使用。但是，要在生产服务器上启用JFR，您需要获得商业许可。

&emsp;&emsp;设置Java以运行连续飞行记录。连续飞行记录是JFR事件的循环缓冲区。如果应用程序遇到问题，您可以转储运行最后一小时的数据。JFR事件对于调试从内存泄漏到网络错误、CPU使用率过高、线程阻塞等各种问题非常有帮助。

&emsp;&emsp;连续飞行记录的运行开销非常低。有关制作连续Java飞行记录的详细信息，请参阅如何制作飞行记录。

```bash
$ java -XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:FlightRecorderOptions=defaultrecording=true,dumponexit=true,dumponexitpath=/path/to/dump.jfr YourApplication
```

