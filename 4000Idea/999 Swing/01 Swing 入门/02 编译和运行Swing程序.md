# 编译和运行Swing程序

&emsp;&emsp;本节将介绍如何从命令行编译和运行Swing应用程序。有关使用NetBeans IDE编译和运行Swing应用程序的信息，请参阅[在NetBeans IDE中运行教程示例](https://docs.oracle.com/javase/tutorial/information/examples.html)。编译说明适用于所有Swing程序，包括小应用程序和大型应用程序。以下是您需要遵循的步骤：

- 如果您还没有安装，请安装最新版本的Java SE平台。
- 创建一个使用Swing组件的程序。
- 编译该程序。
- 运行该程序。

## 安装最新版本的Java SE平台

&emsp;&emsp;您可以从http://www.oracle.com/technetwork/java/javase/downloads/index.html免费下载最新版本的JDK。

## 创建一个使用Swing组件的程序。

&emsp;&emsp;您可以使用我们提供的一个简单程序，名为HelloWorldSwing，它会弹出如下图所示的GUI。该程序只有一个文件，即[HelloWorldSwing.java](https://docs.oracle.com/javase/tutorial/displayCode.html?code=https://docs.oracle.com/javase/tutorial/uiswing/examples/start/HelloWorldSwingProject/src/start/HelloWorldSwing.java)。当您保存此文件时，必须完全匹配其名称的拼写和大小写。

&emsp;&emsp;HelloWorldSwing.java示例和我们所有的 Swing 教程示例一样，都是在一个包内创建的。如果您查看源代码，会在文件开头看到以下行：

```java
package start;
```

&emsp;&emsp;这意味着您必须将HelloWorldSwing.java文件放在一个名为start的目录中。您可以从start目录上面的目录编译和运行示例。使用Swing组件课程中的教程示例位于components包内，而编写事件监听器课程中的示例位于events包内等等。如需更多信息，建议查看[Packages](https://docs.oracle.com/javase/tutorial/java/package/index.html)课程。

![Screen shot of HelloWorldSwing application](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/1helloworldswing.png)

## 编译程序

&emsp;&emsp;您的下一步是编译程序。要编译示例，请从 [HelloWorldSwing.java](https://docs.oracle.com/javase/tutorial/displayCode.html?code=https://docs.oracle.com/javase/tutorial/uiswing/examples/start/HelloWorldSwingProject/src/start/HelloWorldSwing.java) 文件上面的目录中执行以下操作：

```bash
javac start/HelloWorldSwing.java
```

&emsp;&emsp;如果您愿意，可以在start目录中编译示例：

```bash
javac HelloWorldSwing.java
```

&emsp;&emsp;但是你必须记得离开start目录才能执行程序。

&emsp;&emsp;如果您无法编译，请确保您正在使用Java平台最新版本中的编译器。 您可以使用以下命令验证编译器或Java运行时环境（JRE）的版本：

```bash
javac -version
java -version
```

&emsp;&emsp;一旦您更新了JDK，您应该能够在不进行更改的情况下使用本教程中的程序。另一个常见错误是安装JRE而不是编译这些程序所需的完整Java开发工具包（JDK）。请参考[入门指南](https://docs.oracle.com/javase/tutorial/getStarted/index.html)以帮助解决遇到的任何编译问题。另一个资源是[Java™ SE 6桌面技术故障排除指南](https://www.oracle.com/technetwork/java/javase/index-142560.html)。

## 运行程序

&emsp;&emsp;成功编译程序后，您可以运行它。从start目录的上一级目录：

```bash
java start.HelloWorldSwing
```

