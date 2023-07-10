# JarClassLoader类

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/jarclassloader.html)



---



&emsp;&emsp;`JarClassLoader`类扩展了`java.net.URLClassLoader`。顾名思义，`URLClassLoader`设计用于加载通过搜索一组`URL`访问的类和资源。这些`URL`可以指向目录或`JAR`文件。

&emsp;&emsp;除了子类化 `URLClassLoader` 之外，`JarClassLoader` 还利用了另外两个新的 `JAR` 相关 `API` 的特性，即 `java.util.jar` 包和 `java.net.JarURLConnection` 类。在本节中，我们将详细介绍 `JarClassLoader` 的构造函数和两个方法。



---



##### JarClassLoader构造函数

&emsp;&emsp;构造函数将`java.net.URL`的一个实例作为参数。传递给该构造函数的`URL`将在`JarClassLoader`的其他地方使用，以找到要加载类的`JAR`文件。

```java
public JarClassLoader(URL url) {
    super(new URL[] { url });
    this.url = url;
}
```

&emsp;&emsp;`URL`对象被传递给超类`URLClassLoader`的构造函数，该构造函数以`URL[]`数组而非单个`URL`实例作为参数。



---



##### getMainClassName方法

&emsp;&emsp;一旦使用JAR捆绑应用程序的`URL`构建了一个`JarClassLoader`对象，它将需要一种方法来确定JAR文件中的哪个类是应用程序的入口点。这就是`getMainClassName`方法的工作：

```java
public String getMainClassName() throws IOException {
    URL u = new URL("jar", "", url + "!/");
    JarURLConnection uc = (JarURLConnection)u.openConnection();
    Attributes attr = uc.getMainAttributes();
    return attr != null
                   ? attr.getValue(Attributes.Name.MAIN_CLASS)
                   : null;
}
```

&emsp;&emsp;您可能还记得在上一课中，`JAR`捆绑应用程序的入口点是由`JAR`文件清单中的`Main-Class`头指定的。为了理解`getMainClassName`如何访问`Main-Class`头文件的值，让我们详细看看该方法，特别注意它使用的新的`JAR`处理特性：



---



##### JarURLConnection类和JAR URL

&emsp;&emsp;`getMainClassName`方法使用`java.net.JarURLConnection`类指定的`JAR URL`格式。`JAR`文件`URL`的语法如下面的示例所示：

```bash
jar:http://www.example.com/jarfile.jar!/
```

&emsp;&emsp;结束符 `!/` 分隔符表示 `URL` 指向整个 `JAR` 文件。分隔符后面的内容指的是特定的`JAR`文件内容，如本示例：

```bash
jar:http://www.example.com/jarfile.jar!/mypackage/myclass.class
```

&emsp;&emsp;`getMainClassName`方法的第一行是:

```bash
URL u = new URL("jar", "", url + "!/");
```

&emsp;&emsp;该语句构造一个新的代表`JAR URL`的`URL`对象，在创建`JarClassLoader`实例时使用的`URL`上添加`!/`分隔符。



---



##### java.net.JarURLConnection类

&emsp;&emsp;该类表示应用程序与`JAR`文件之间的通信链接。它拥有访问`JAR`文件清单的方法。`getMainClassName`的第二行是:

```java
JarURLConnection uc = (JarURLConnection)u.openConnection();
```

&emsp;&emsp;在该语句中，第一行创建的`URL`实例打开了一个`URLConnection`。然后`URLConnection`实例被转换为`JarURLConnection`，这样它就可以利用`JarURLConnection`的`JAR`处理功能。



---



##### 获取清单属性：java.util.jar.Attributes

&emsp;&emsp;当 `JarURLConnection` 打开到 `JAR` 文件时，您可以使用 `JarURLConnection` 的 `getMainAttributes` 方法访问 `JAR` 文件清单中的头信息。该方法返回 `java.util.jar.Attributes` 的一个实例，该类将 `JAR` 文件清单中的头信息名称与其相关的字符串值进行映射。`getMainClassName` 中的第三行创建了一个 `Attributes` 对象：

```bash
Attributes attr = uc.getMainAttributes();
```

&emsp;&emsp;要获取清单`Main-Class`标头的值，`getMainClassName`的第四行会调用`Attributes.getValue`方法：

```java
return attr != null
               ? attr.getValue(Attributes.Name.MAIN_CLASS)
               : null;
```

&emsp;&emsp;该方法的参数`Attributes.Name.MAIN_CLASS`指定了你想要的`Main-Class`头的值。(`Attributes.Name`类还提供了静态字段，如`MANIFEST_VERSION`、`CLASS_PATH`和`SEALED`，用于指定其他标准清单头）。



---



##### invokeClass方法

&emsp;&emsp;我们已经看到`JarURLClassLoader`如何识别`JAR`捆绑应用程序中的主类。最后要考虑的方法`JarURLClassLoader.invokeClass`可以调用主类来启动`JAR`捆绑应用程序：

```java
public void invokeClass(String name, String[] args)
    throws ClassNotFoundException,
           NoSuchMethodException,
           InvocationTargetException
{
    Class c = loadClass(name);
    Method m = c.getMethod("main", new Class[] { args.getClass() });
    m.setAccessible(true);
    int mods = m.getModifiers();
    if (m.getReturnType() != void.class || !Modifier.isStatic(mods) ||
        !Modifier.isPublic(mods)) {
        throw new NoSuchMethodException("main");
    }
    try {
        m.invoke(null, new Object[] { args });
    } catch (IllegalAccessException e) {
        // This should not happen, as we have disabled access checks
    }
}
```

&emsp;&emsp;`invokeClass`方法需要两个参数：应用程序入口点类的名称和传递给入口点类`main`方法的字符串参数数组。首先，加载主类：

```java
Class c = loadClass(name);
```

&emsp;&emsp;`loadClass`方法继承于`java.lang.ClassLoader`。

&emsp;&emsp;主类加载完成后，`java.lang.reflect`包中的反射`API`将用于向类传递参数并启动类。您可以参考反射`API`教程来了解反射。