# 运行JAR包的软件

&emsp;&emsp;现在你已经学会了如何创建`JAR`文件，那么你如何实际运行你打包的代码呢？考虑一下这些情况：

- 你的JAR文件包含一个将在浏览器中运行的小程序。
- 你的JAR文件包含一个要从命令行启动的应用程序。
- 你的JAR文件包含你想用作扩展的代码。

&emsp;&emsp;本节将涵盖前两种情况。教程中关于[扩展机制](https://docs.oracle.com/javase/tutorial/ext/index.html)的单独线索涵盖了使用JAR文件作为扩展的情况。



---



##### 以JAR文件打包的Applets

&emsp;&emsp;要从一个`HTML`文件中启动任何小程序以便在浏览器中运行，你可以使用小程序标签。欲了解更多信息，请看`Java Applets`[课程](https://docs.oracle.com/javase/tutorial/deployment/applet/index.html)。如果小程序被捆绑成一个`JAR`文件，你需要做的唯一不同的事情就是使用`archive`参数来指定`JAR`文件的相对路径。

&emsp;&emsp;作为一个例子，使用`TicTacToe`演示小程序。显示该小程序的`HTML`文件中的小程序标签可以这样标记：

```html
<applet code=TicTacToe.class 
        width="120" height="120">
</applet>
```

&emsp;&emsp;如果`TicTacToe`演示被打包在一个名为`TicTacToe.jar`的`JAR`文件中，你可以通过添加一个归档参数来修改小程序标签：

```html
<applet code=TicTacToe.class 
        archive="TicTacToe.jar"
        width="120" height="120">
</applet>
```

&emsp;&emsp;`archive`参数指定了包含`TicTacToe.class`的`JAR`文件的相对路径。在这个例子中，我们假设`JAR`文件和`HTML`文件在同一个目录下。如果它们不在一起，你必须在归档参数的值中包括`JAR`文件的相对路径。例如，如果`JAR`文件在`HTML`文件下面的一个名为`applets`的目录中，那么`applet`标签将看起来像这样：

```html
<applet code=TicTacToe.class 
        archive="applets/TicTacToe.jar"
        width="120" height="120">
</applet>
```



---



##### 作为应用程序的JAR文件

&emsp;&emsp;你可以用`Java`启动器（`java`命令）运行`JAR`打包的应用程序。基本命令是

```bash
$ java -jar jar-file
```

&emsp;&emsp;`-jar`标志告诉启动器，应用程序是以`JAR`文件格式打包的。你只能指定一个`JAR`文件，该文件必须包含所有应用程序的特定代码。

&emsp;&emsp;在你执行这个命令之前，确保运行环境有关于`JAR`文件中哪个类是应用程序的入口点的信息。

&emsp;&emsp;为了表明哪个类是应用程序的入口，你必须在`JAR`文件的清单中添加一个`Main-Class`头。该头文件的形式为：

```bash
Main-Class: classname
```

&emsp;&emsp;头部的值，`classname`，是作为应用程序入口点的类的名称。

&emsp;&emsp;欲了解更多信息，请参见[设置应用程序的入口点](https://docs.oracle.com/javase/tutorial/deployment/jar/appman.html)一节。

&emsp;&emsp;当清单文件中设置了`Main-Class`后，你可以从命令行中运行应用程序：

```bash
$ java -jar app.jar
```

&emsp;&emsp;要从另一个目录下的`JAR`文件中运行应用程序，你必须指定该目录的路径： `java -jar path/app.jar`



---



[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/run.html)