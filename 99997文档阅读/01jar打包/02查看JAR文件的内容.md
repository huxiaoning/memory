# 查看JAR文件的内容

&emsp;&emsp;查看JAR文件内容的命令的基本格式是：

```bash
$ jar tf jar-file
```

&emsp;&emsp;让我们看一下这个命令中使用的选项和参数：

- `t`选项表示你想查看`JAR`文件的内容表。
- `f`选项表示要查看的`JAR`文件的内容是在命令行中指定的。
- `jar-file`参数是你想查看其内容的`JAR`文件的路径和名称。

&emsp;&emsp;`t`和`f`选项可以按任一顺序出现，但它们之间不能有任何空格。

&emsp;&emsp;这个命令将显示`JAR`文件的目录到`stdout`。

&emsp;&emsp;你可以选择添加`verbose`选项，`v`，以在输出中产生关于文件大小和最后修改日期的额外信息。



---



##### 一个例子

&emsp;&emsp;让我们使用`Jar`工具来列出我们在上一节创建的`TicTacToe.jar`文件的内容：

```bash
$ jar tf TicTacToe.jar
```

&emsp;&emsp;该命令将`JAR`文件的内容显示到`stdout`：

```bash
META-INF/MANIFEST.MF
TicTacToe.class
audio/
audio/beep.au
audio/ding.au
audio/return.au
audio/yahoo1.au
audio/yahoo2.au
images/
images/cross.gif
images/not.gif
```

&emsp;&emsp;`JAR`文件包含`TicTacToe`类文件以及音频和图像目录，正如预期的那样。输出结果还显示，`JAR`文件包含一个默认的清单文件，`META-INF/MANIFEST.MF`，它是由`JAR`工具自动放置在存档中的。更多信息，请参见理解默认清单部分。

&emsp;&emsp;所有的路径名都用正斜线显示，不管你使用的是什么平台或操作系统。`JAR`文件中的路径总是相对的；例如，你永远不会看到一个以`C:`开头的路径。

&emsp;&emsp;如果你使用`v`选项，`JAR`工具将显示额外的信息：

```bash
$ jar tvf TicTacToe.jar
```

&emsp;&emsp;例如，`TicTacToe JAR`文件的粗略输出看起来类似于这样：

```bash
    68 Thu Nov 01 20:00:40 PDT 2012 META-INF/MANIFEST.MF
   553 Mon Sep 24 21:57:48 PDT 2012 TicTacToe.class
  3708 Mon Sep 24 21:57:48 PDT 2012 TicTacToe.class
  9584 Mon Sep 24 21:57:48 PDT 2012 TicTacToe.java
     0 Mon Sep 24 21:57:48 PDT 2012 audio/
  4032 Mon Sep 24 21:57:48 PDT 2012 audio/beep.au
  2566 Mon Sep 24 21:57:48 PDT 2012 audio/ding.au
  6558 Mon Sep 24 21:57:48 PDT 2012 audio/return.au
  7834 Mon Sep 24 21:57:48 PDT 2012 audio/yahoo1.au
  7463 Mon Sep 24 21:57:48 PDT 2012 audio/yahoo2.au
   424 Mon Sep 24 21:57:48 PDT 2012 example1.html
     0 Mon Sep 24 21:57:48 PDT 2012 images/
   157 Mon Sep 24 21:57:48 PDT 2012 images/cross.gif
   158 Mon Sep 24 21:57:48 PDT 2012 images/not.gif
```



[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/view.html)

