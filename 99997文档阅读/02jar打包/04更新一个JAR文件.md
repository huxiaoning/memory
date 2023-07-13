# 更新一个JAR文件

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/update.html)



---



&emsp;&emsp;`Jar`工具提供了一个`u`选项，你可以通过修改其清单或添加文件来更新现有JAR文件的内容。

&emsp;&emsp;添加文件的基本命令有这样的格式：

```bash
$ jar uf jar-file input-file(s)
```

&emsp;&emsp;在这个命令中：

- `u`选项表示你想更新一个现有的JAR文件。
- `f`选项表示要更新的`JAR`文件是在命令行中指定的。
- `jar-file`是要被更新的现有`JAR`文件。
- `input-file(s)`是一个以空格分隔的列表，包含一个或多个你想添加到`JAR`文件的文件。

&emsp;&emsp;归档文件中任何与被添加的文件有相同路径名的文件将被覆盖。

&emsp;&emsp;当创建一个新的`JAR`文件时，你可以选择使用`-C`选项来表示目录的改变。欲了解更多信息，请参见创建JAR文件一节。



---

##### 例子

&emsp;&emsp;回顾一下，`TicTacToe.jar`有这些内容：

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

&emsp;&emsp;假设你想把`images/new.gif`这个文件添加到`JAR`文件中。你可以通过在`images`目录的父目录下发出这个命令来实现：

```bash
$ jar uf TicTacToe.jar images/new.gif
```

&emsp;&emsp;修订后的`JAR`文件会有这样的目录：

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
images/new.gif
```

&emsp;&emsp;你可以使用`-C`选项在执行命令时 "改变目录"。比如说

```bash
$ jar uf TicTacToe.jar -C images new.gif
```

&emsp;&emsp;这个命令将在添加`new.gif`到`JAR`文件之前改变到`images`目录。当`new.gif`被添加到归档文件时，`images`目录将不包括在new.gif的路径名中，导致目录看起来像这样：

```bash
META-INF/MANIFEST.MF
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
new.gif
```


