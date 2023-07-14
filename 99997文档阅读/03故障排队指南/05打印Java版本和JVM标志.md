# 打印Java版本和JVM标志

&emsp;&emsp;在提交 Java Bug 或从论坛寻求帮助之前，请准备好日志文件中的基本信息。例如，打印出Java版本和使用的JVM标志很有帮助。

&emsp;&emsp;如果您的应用程序以脚本启动，只需运行`java -version`即可打印Java版本，并在执行前打印命令行。另一种方法是在JVM参数中添加`-XX+PrintCommandLineFlags`和`-showversion`。