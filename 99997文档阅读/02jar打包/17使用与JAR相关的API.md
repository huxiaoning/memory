# 使用与JAR相关的API

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/apiindex.html)



---



&emsp;&emsp;`Java`平台包含多个用于`JAR`文件的类。其中一些`API`是:

- `java.util.jar`包
- `java.net.JarURLConnection`类
- `java.net.URLClassLoader`类

&emsp;&emsp;为了让您了解这些新`API`所带来的可能性，本课将引导您了解一个名为`JarRunner`的示例应用程序的内部运作。



---



##### 示例 - JarRunner应用程序

&emsp;&emsp;通过在命令行中指定 `JAR` 文件的 `URL`，`JarRunner` 使您能够运行捆绑在 `JAR` 文件中的应用程序。例如，如果名为 `TargetApp` 的应用程序捆绑在 `http://www.example.com/TargetApp.jar` 的 `JAR `文件中，您可以使用此命令运行该应用程序：

```bash
$ java JarRunner http://www.example.com/TargetApp.jar
```

&emsp;&emsp;为了使`JarRunner`正常工作，它必须能够执行以下任务，所有这些任务都可以通过使用新的`API`来完成：

- 访问远程`JAR`文件并与其建立通信连接。
- 检查`JAR`文件的清单，查看归档文件中哪个类是主类。
- 加载`JAR`文件中的类。

&emsp;&emsp;`JarRunner`应用程序由两个类组成：`JarRunner`和`JarClassLoader`。`JarRunner`将大部分`JAR`处理任务委托给`JarClassLoader`类。`JarClassLoader`扩展了`java.net.URLClassLoader`类。在继续本课之前，你可以浏览`JarRunner`和`JarClassLoader`类的源代码：

- [`JarRunner.java`](https://docs.oracle.com/javase/tutorial/deployment/jar/examples/JarRunner.java)
- [`JarClassLoader.java`](https://docs.oracle.com/javase/tutorial/deployment/jar/examples/JarClassLoader.java)

