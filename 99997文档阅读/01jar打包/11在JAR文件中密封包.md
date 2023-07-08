# 在JAR文件中密封包

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/sealman.html)



---



&emsp;&emsp;`JAR`文件中的包可以选择密封，这意味着该包中定义的所有类必须归档在同一个`JAR`文件中。例如，你可能想密封一个包，以确保你的软件中的类之间的版本一致性。

&emsp;&emsp;你可以通过在清单中添加密封头来密封`JAR`文件中的包，密封头的一般形式是：

```bash
Name: myCompany/myPackage/
Sealed: true
```

&emsp;&emsp;值`myCompany/myPackage/`是要密封的包的名称。

&emsp;&emsp;注意，软件包的名称必须以`/`结尾。



---



##### 一个例子

&emsp;&emsp;我们想在`JAR`文件`MyJar.jar`中密封两个包`firstPackage`和`secondPackage`。

&emsp;&emsp;我们首先创建一个名为`Manifest.txt`的文本文件，内容如下：

```bash
Name: myCompany/firstPackage/
Sealed: true

Name: myCompany/secondPackage/
Sealed: true
```

> 警告：文本文件必须以换行或回车结束。如果最后一行不是以换行或回车结束，将不能被正确解析。

&emsp;&emsp;然后我们通过输入以下命令创建一个名为`MyJar.jar`的`JAR`文件：

```bash
$ jar cfm MyJar.jar Manifest.txt MyPackage/*.class
```

&emsp;&emsp;这将创建具有以下内容的清单的JAR文件：

```bahs
Manifest-Version: 1.0
Created-By: 1.7.0_06 (Oracle Corporation)
Name: myCompany/firstPackage/
Sealed: true
Name: myCompany/secondPackage/
Sealed: true
```



---



##### 封闭JAR文件

&emsp;&emsp;如果你想保证一个包中的所有类都来自同一个代码源，可以使用`JAR`密封。一个密封的JAR指定所有由该JAR定义的包都是密封的，除非在每个包的基础上被重写。

&emsp;&emsp;要密封一个`JAR`文件，请使用值为 `true`的密封清单头。比如说、

```bash
Sealed: true
```

&emsp;&emsp;指定此存档中的所有包都是密封的，除非在清单条目中用 `Sealed` 属性明确地覆盖特定的包。