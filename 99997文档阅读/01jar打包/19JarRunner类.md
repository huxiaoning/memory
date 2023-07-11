# JarRunner类

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/jarrunner.html)



---



&emsp;&emsp;通过这种形式的命令启动`JarRunner`应用程序：

```bash
$ java JarRunner url [arguments]
```

&emsp;&emsp;在上一节中，我们看到了`JarClassLoader`如何从给定的`URL`中识别并加载`JAR`捆绑应用程序的主类。因此，为了完成`JarRunner`应用程序，我们需要能够从命令行获取`URL`和任何参数，并将它们传递给`JarClassLoader`的实例。这些任务属于`JarRunner`类，即`JarRunner`应用程序的入口点。

&emsp;&emsp;它首先根据命令行中指定的`URL`创建一个`java.net.URL`对象：

```java
public static void main(String[] args) {
    if (args.length < 1) {
        usage();
    }
    URL url = null;
    try {
        url = new URL(args[0]);
    } catch (MalformedURLException e) {
        fatal("Invalid URL: " + args[0]);
    }
```

&emsp;&emsp;如果`args.length < 1`，表示命令行中没有指定`URL`，因此会打印一条使用信息。如果第一个命令行参数是一个好的`URL`，将创建一个新的`URL`对象来表示它。

&emsp;&emsp;接下来，`JarRunner`创建一个新的`JarClassLoader`实例，将命令行中指定的`URL`传递给构造函数：

```java
JarClassLoader cl = new JarClassLoader(url);
```

&emsp;&emsp;正如我们在上一节中所看到的，`JarRunner`通过`JarClassLoader`接入JAR处理API。

&emsp;&emsp;传递给`JarClassLoader`构造函数的`URL`是您要运行的`JAR`捆绑应用程序的`URL`。接下来，`JarRunner`调用类加载器的`getMainClassName`方法来识别应用程序的入口点类：

```java
String name = null;
try {
    name = cl.getMainClassName();
} catch (IOException e) {
    System.err.println("I/O error while loading JAR file:");
    e.printStackTrace();
    System.exit(1);
}
if (name == null) {
    fatal("Specified jar file does not contain a 'Main-Class'" +
          " manifest attribute");
}
```

&emsp;&emsp;关键语句用粗体标出。其他语句用于错误处理。

&emsp;&emsp;一旦`JarRunner`确定了应用程序的入口点类，就只剩下两个步骤：向应用程序传递参数和实际启动应用程序。`JarRunner`通过以下代码执行这些步骤：

```java
// Get arguments for the application
String[] newArgs = new String[args.length - 1];
System.arraycopy(args, 1, newArgs, 0, newArgs.length);
// Invoke application's main class
try {
    cl.invokeClass(name, newArgs);
} catch (ClassNotFoundException e) {
    fatal("Class not found: " + name);
} catch (NoSuchMethodException e) {
    fatal("Class does not define a 'main' method: " + name);
} catch (InvocationTargetException e) {
    e.getTargetException().printStackTrace();
    System.exit(1);
}
```

&emsp;&emsp;回想一下，第一个命令行参数是JAR捆绑应用程序的URL。因此，要传递给该应用程序的任何参数都在args数组的元素1及其后。`JarRunner`接收这些元素，并创建一个名为`newArgs`的新数组传递给应用程序（上文粗体行）。然后，`JarRunner`将入口点的类名和新参数列表传递给`JarClassLoader`的`invokeClass`方法。正如我们在上一节中看到的，`invokeClass`将加载应用程序的入口点类，传递给它任何参数，并启动应用程序。

