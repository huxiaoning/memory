# 创建JAR文件

创建JAR文件的命令的基本格式是：

```bash
$ jar cf jar-file input-file(s)
```

该命令中使用的选项和参数是：

- `c`选项表示你想创建一个JAR文件。
- `f`选项表示你希望输出到一个文件而不是`stdout`。
- `jar-file`是你希望产生的JAR文件的名称。你可以为一个JAR文件使用任何文件名。按照惯例，JAR文件名有一个`.jar`扩展名，尽管这不是必须的。
- `input-file(s)`参数是一个以空格分隔的列表，其中包括一个或多个你想包含在JAR文件中的文件。`input-file(s)`参数可以包含通配符*符号。如果任何一个 "输入文件 "是目录，这些目录的内容将被递归地添加到JAR归档中。

`c`和`f`选项可以按任何顺序出现，但它们之间不能有任何空格。

该命令将生成一个压缩的JAR文件，并将其放在当前目录中。该命令还将为JAR归档文件生成一个默认的清单文件。

> 注意：
> JAR文件中的元数据，如条目名称、注释和清单内容，必须用`UTF8`编码。



---



你可以在基本命令的`cf`选项中添加任何这些附加选项：

<center>jar命令选项</center>

| 选项     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| `v`      | 当JAR文件正在构建时，在`stdout`上产生`verbose`输出。冗长的输出告诉你每个文件的名称，因为它被添加到JAR文件中。 |
| `0` (零) | 表示你不希望JAR文件被压缩。                                  |
| `M`      | 表示不应制作默认清单文件。                                   |
| `m`      | 用于包含现有清单文件中的清单信息。使用此选项的格式为：<br />`jar cmf jar-file existing-manifest input-file(s)`<br />关于这个选项的更多信息，请参见修改清单文件。<br />警告：清单必须以换行或回车结束。如果最后一行没有以换行或回车结束，则无法正确解析。 |
| `-C`     | 要在执行命令的过程中改变目录。请看下面的例子。               |

> 当你创建一个JAR文件时，创建的时间会存储在JAR文件中。因此，即使JAR文件的内容没有变化，当你多次创建一个JAR文件时，所产生的文件也不完全相同。当你在构建环境中使用JAR文件时，你应该注意到这一点。建议你使用清单文件中的版本信息，而不是创建时间，来控制JAR文件的版本。参见设置软件包版本信息部分。



---



##### 一个例子

让我们看一个例子。一个简单的`TicTacToe`小程序。你可以从Java SE Downloads下载JDK Demos and Samples捆绑包来查看这个小程序的源代码。这个演示包含具有这种结构的类文件、音频文件和图像：

![TicTacToe文件夹层次结构](https://docs.oracle.com/javase/tutorial/figures/deployment/jar/ticTacToeJar.gif)

<center>TicTacToe文件夹层次结构</center>

`audio`和`images`子目录包含小程序(`applet`)使用的声音文件和`GIF`图像。

当你在网上下载整个教程时，你可以从`jar/examples`目录中获得所有这些文件。要把这个演示打包成一个名为`TicTacToe.jar`的JAR文件，你可以在`TicTacToe`目录中运行这个命令：

```bash
$ jar cvf TicTacToe.jar TicTacToe.class audio images
```

`audio`和`images`参数代表目录，所以`Jar`工具将递归地把它们和它们的内容放在`JAR`文件中。生成的`JAR`文件`TicTacToe.jar`将被放置在当前目录中。因为该命令使用了`v`选项进行粗略的输出，所以当你运行该命令时，你会看到类似于这样的输出：

```bash
added manifest
adding: TicTacToe.class(in = 3705) (out= 2232)(deflated 39%)
adding: audio/(in = 0) (out= 0)(stored 0%)
adding: audio/beep.au(in = 4032) (out= 3572)(deflated 11%)
adding: audio/yahoo2.au(in = 7463) (out= 4607)(deflated 38%)
adding: audio/yahoo1.au(in = 7834) (out= 6985)(deflated 10%)
adding: audio/ding.au(in = 2566) (out= 2055)(deflated 19%)
adding: audio/return.au(in = 6558) (out= 4401)(deflated 32%)
adding: images/(in = 0) (out= 0)(stored 0%)
adding: images/cross.gif(in = 157) (out= 160)(deflated -1%)
adding: images/not.gif(in = 158) (out= 161)(deflated -1%)
```

你可以从这个输出中看到，`JAR`文件`TicTacToe.jar`被压缩了。`Jar`工具默认情况下是压缩文件的。你可以通过使用`0`（零）选项来关闭压缩功能，这样，命令看起来就像：

```bash
$ jar cvf0 TicTacToe.jar TicTacToe.class audio images
```

你可能想避免压缩，例如，为了提高浏览器加载`JAR`文件的速度。未压缩的`JAR`文件通常可以比压缩的文件更快加载，因为在加载过程中不需要解压文件。然而，对于较大的、未压缩的文件来说，在网络上的下载时间可能会更长，这也是一种折中。

`Jar`工具将接受使用通配符`*`符号的参数。只要`TicTacToe`目录中没有任何不需要的文件，你就可以使用这个替代命令来构造`JAR`文件：

```bash
$ jar cvf TicTacToe.jar *
```

虽然在粗略的输出中没有显示，但 Jar 工具会自动向 JAR 归档文件中添加一个清单文件，路径名为 META-INF/MANIFEST.MF。有关清单文件的信息，请参见 Working with Manifest Files：有关清单文件的信息，请参见 "使用清单文件：基础知识 "部分。

这里可以看看生成的清单文件是什么内容：

```bash
# 复制到/tmp目录执行
$ unzip TicTacToe.jar
$ cd META-INF/
$ cat MANIFEST.MF
Manifest-Version: 1.0
Created-By: 1.8.0_65 (Oracle Corporation)

```

在上面的例子中，存档的文件保留了它们的相对路径名称和目录结构。`Jar`工具提供了`-C`选项，你可以用它来创建一个`JAR`文件，其中不保留归档文件的相对路径。它是以`TAR`的`-C`选项为模型的。

举个例子，假设你想把`TicTacToe`演示所使用的音频文件和GIF图像放到一个`JAR`文件中，而且你希望所有的文件都在顶层，没有目录层次。你可以通过在图像和音频目录的父目录下发出这个命令来实现：

```bash
$ jar cf ImageAudio.jar -C images . -C audio .
```

该命令的`-C images`部分指示`Jar`工具进入`images`目录，而`-C images`后面的`.`则指示`Jar`工具对该目录中的所有内容进行存档。命令的`-C audio .` 部分对音频目录做了同样的处理。由此产生的`JAR`文件会有这样的内容表：

```bash
META-INF/MANIFEST.MF
cross.gif
not.gif
beep.au
ding.au
return.au
yahoo1.au
yahoo2.au
```

