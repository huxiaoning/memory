# OOM时转储堆栈

&emsp;&emsp;在JVM标志中添加`-XX:+HeapDumpOnOutOfMemoryError`： 如果应用程序遇到`OutOfMemoryError`，`-XX:+HeapDumpOnOutOfMemoryError`标志将Java堆转储到磁盘。使用jhat Utility工具检查Java堆，找出占用空间最大的对象，然后检查不再使用但仍存活的对象。

> 与核心文件一样，堆转储也可能非常大，尤其是在运行较大的Java堆时。

&emsp;&emsp;再次，想一想如果应用程序遇到`OutOfMemoryError`，你会怎么做。你想在出错时检查堆吗？在这种情况下，默认打开这个标志，这样当应用程序遇到意外的`OutOfMemoryError`时，你就可以得到这些数据。





&emsp;&emsp;在默认情况下，生成的堆转储文件通常被保存在应用程序的工作目录下，也就是启动应用程序的目录。转储文件的文件名通常包含时间戳和进程ID等信息，以帮助唯一标识转储文件。

&emsp;&emsp;如果你想自定义堆转储文件的位置，可以使用`-XX:HeapDumpPath`参数来指定路径。例如，你可以使用以下命令将堆转储文件保存在特定的目录中：

```bash
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dump/directory/
```

&emsp;&emsp;请注意，确保应用程序运行的用户对指定的目录具有写权限，否则可能会导致生成堆转储文件失败。