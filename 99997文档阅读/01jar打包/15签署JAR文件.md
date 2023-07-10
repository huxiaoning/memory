# 签署JAR文件

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/signing.html)



---



&emsp;&emsp;你使用`JAR`签名和验证工具来签署`JAR`文件，并在签名上盖上时间戳。你通过使用`jarsigner`命令来调用`JAR`签名和验证工具，所以我们会把它简称为 "`Jarsigner`"。

&emsp;&emsp;要签署一个`JAR`文件，你必须先有一个私钥。私钥及其相关的公钥证书被存储在有密码保护的数据库中，称为钥匙库。一个钥匙库可以保存许多潜在签名者的钥匙。钥匙库中的每一把钥匙都可以通过别名来识别，而别名通常是拥有该钥匙的签名人的名字。例如，属于`Rita Jones`的钥匙可能有一个别名 "`rita`"。

&emsp;&emsp;签署`JAR`文件的命令的基本形式是

```bash
$ jarsigner jar-file alias
```

- `jar-file`是要被签名的`JAR`文件的路径名。
- `alias`是识别将被用于签署`JAR`文件的私钥的别名，以及该密钥的相关证书。

&emsp;&emsp;`Jarsigner`工具会提示你输入钥匙库和别名的密码。

&emsp;&emsp;该命令的基本形式是假定要使用的密钥库在你的主目录下一个名为`.keystore`的文件中。它将创建签名和签名块文件，名称分别为`x.SF`和`x.DSA`，其中`x`是别名的前八个字母，全部转换为大写。这个基本命令将用签名后的`JAR`文件覆盖原始`JAR`文件。

&emsp;&emsp;在实践中，你可能想使用一个或多个可用的命令选项。例如，我们鼓励在签名上盖上时间戳，以便用于部署应用程序的任何工具能够验证用于签署`JAR`文件的证书在文件被签署时是有效的。如果不包括时间戳，`Jarsigner`工具会发出警告。

&emsp;&emsp;选项在`jar-file`路径名之前。下表描述了可用的选项：

<center>Jarsigner命令选项</center>

| 选项                           | 描述 |
| ------------------------------ | ---- |
| `-keystore url`                |      |
| `-sigfile file`                |      |
| `-signedjar file`              |      |
| `-tsa url`                     |      |
| `-tsacert` *alias*             |      |
| `-altsigner class`             |      |
| `-altsignerpath classpathlist` |      |

