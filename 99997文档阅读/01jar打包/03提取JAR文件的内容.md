# 提取JAR文件的内容

&emsp;&emsp;用来提取`JAR`文件内容的基本命令是：

```bash
$ jar xf jar-file [archived-file(s)]
```

&emsp;&emsp;我们来看看这个命令中的选项和参数：

- `x`选项表示你想从`JAR`归档中提取文件。
- `f`选项表示要提取文件的JAR文件是在命令行中指定的，而不是通过`stdin`。
- `jar-file`参数是`JAR`文件的文件名（或路径和文件名），要从中提取文件。
- `archived-file(s)`是一个可选的参数，包括一个以空格分隔的列表，列出了要从归档文件中提取的文件。如果这个参数不存在，Jar工具将提取档案中的所有文件。

&emsp;&emsp;像往常一样，`x`和`f`选项在命令中出现的顺序并不重要，但它们之间一定不能有空格。

&emsp;&emsp;在提取文件时，`Jar`工具会复制所需的文件并将其写入当前目录，再现文件在归档中的目录结构。原始`JAR`文件保持不变。

> 请注意：当它提取文件时，`Jar`工具将覆盖任何与被提取文件具有相同路径名称的现有文件。



---

##### 一个例子

&emsp;&emsp;让我们从我们在前面几节中一直使用的`TicTacToe JAR`文件中提取一些文件。回顾一下，`TicTacToe.jar`的内容是：

```bash
META-INF/MANIFEST.MF
TicTacToe.class
TicTacToe.class
TicTacToe.java
audio/
audio/beep.au
audio/ding.au
audio/return.au
audio/yahoo1.au
audio/yahoo2.au
example1.html
images/
images/cross.gif
images/not.gif
```

&emsp;&emsp;假设你想提取`TicTacToe`类文件和`cross.gif`图像文件。要做到这一点，你可以使用这个命令：

```bash
$ jar xf TicTacToe.jar TicTacToe.class images/cross.gif
```

&emsp;&emsp;这个命令做了两件事：

- 它将`TicTacToe.class`的副本放在当前目录中。
- 它创建了`images`目录（如果它不存在），并在其中放置了`cross.gif`的副本。

&emsp;&emsp;原始的`TicTacToe JAR`文件保持不变。

&emsp;&emsp;可以用同样的方法从`JAR`文件中提取所需的许多文件。当命令没有指定要提取哪些文件时，`Jar`工具会提取存档中的所有文件。例如，你可以用这个命令来提取`TicTacToe`归档文件中的所有文件：

```bash
$ jar xf TicTacToe.jar
```



---



[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/unpack.html)