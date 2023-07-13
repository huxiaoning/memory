# 将类添加到JAR文件的Classpath中

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/downman.html)



---



&emsp;&emsp;你可能需要从一个`JAR`文件中引用其他`JAR`文件中的类。

&emsp;&emsp;例如，在一个典型的情况下，一个小程序被捆绑在一个`JAR`文件中，其清单引用了一个不同的`JAR`文件（或几个不同的`JAR`文件），作为该小程序的实用工具。

&emsp;&emsp;您可以在小程序或应用程序的清单文件中的`Class-Path`头域中指定要包含的类。类路径标头采用以下形式：

```bash
Class-Path: jar1-name jar2-name directory-name/jar3-name
```

&emsp;&emsp;通过在清单中使用`Class-Path`头，您可以避免在调用`Java`运行您的应用程序时指定一个长的`-classpath`标志。

> 注意：`Class-Path`头指向本地网络上的类或`JAR`文件，而不是`JAR`文件内的`JAR`文件或通过互联网协议访问的类。要把`JAR`文件内的类加载到类路径中，你必须编写自定义代码来加载这些类。例如，如果`MyJar.jar`包含另一个名为`MyUtils.jar`的`JAR`文件，你不能使用`MyJar.jar`清单中的`Class-Path`头来加载`MyUtils.jar`中的类到类路径中。



---



##### 一个例子

&emsp;&emsp;我们要把`MyUtils.jar`中的类加载到`MyJar.jar`中使用的类路径。这两个`JAR`文件是在同一个目录下。

&emsp;&emsp;我们首先创建一个名为`Manifest.txt`的文本文件，内容如下：

```bash
Class-Path: MyUtils.jar
```

> 警告：文本文件必须以换行或回车结束。如果最后一行不是以换行或回车结束，将不能被正确解析。

&emsp;&emsp;然后我们通过输入以下命令创建一个名为`MyJar.jar`的`JAR`文件：

```bash
$ jar cfm MyJar.jar Manifest.txt MyPackage/*.class
```

&emsp;&emsp;这将创建具有以下内容的清单的JAR文件：

```bash
Manifest-Version: 1.0
Class-Path: MyUtils.jar
Created-By: 1.7.0_06 (Oracle Corporation)
```

&emsp;&emsp;当你运行`MyJar.jar`时，`MyUtils.jar`中的类现在被加载到类路径中。