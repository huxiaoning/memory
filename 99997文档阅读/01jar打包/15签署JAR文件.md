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

| 选项                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| `-keystore url`                | 如果不想使用 `.keystore` 默认数据库，则指定要使用的密钥存储。 |
| `-sigfile file`                | 指定 `.SF` 和 `.DSA` 文件的基本名称，如果您不希望基本名称取自别名。文件必须仅由大写字母 (A-Z)、数字 (0-9)、连字符 (-) 和下划线 (_) 组成。 |
| `-signedjar file`              | 如果不希望未签名的原始文件被签名文件覆盖，则指定要生成的签名 `JAR` 文件的名称。 |
| `-tsa url`                     | 使用由`URL`确定的时间戳机构（`TSA`）为签名生成一个时间戳。   |
| `-tsacert alias`               | 使用别名标识的`TSA`公钥证书为签名生成时间戳。                |
| `-altsigner class`             | 表示使用另一种签名机制对签名加时间戳。全称类名标识所使用的类。 |
| `-altsignerpath classpathlist` | 提供由`altsigner`选项确定的类的路径以及该类所依赖的任何`JAR`文件。 |



---



##### 例子

&emsp;&emsp;让我们来看几个使用`Jarsigner`工具签署`JAR`文件的例子。在这些示例中，我们将假设如下： 

- 您的别名是 "johndoe"。
- 您要使用的密钥存储在当前工作目录下名为 "mykeys "的文件中。
- 您希望用于签名时间戳的 TSA 位于 http://tsa.url.example.com。

&emsp;&emsp;根据这些假设，您可以使用此命令签署名为 `app.jar` 的 `JAR` 文件：

```bash
$ jarsigner -keystore mykeys -tsa http://tsa.url.example.com app.jar johndoe
```

&emsp;&emsp;系统会提示你输入密钥存储和别名的密码。由于该命令没有使用 `-sigfile` 选项，它创建的 `.SF` 和 `.DSA` 文件将被命名为 `JOHNDOE.SF` 和 `JOHNDOE.DSA`。由于命令没有使用`-signedjar`选项，生成的签名文件将覆盖原始版本的`app.jar`。

&emsp;&emsp;让我们看看如果您使用不同的选项组合会发生什么情况：

```bash
$ jarsigner -keystore mykeys -sigfile SIG -signedjar SignedApp.jar -tsacert testalias app.jar johndoe
```

&emsp;&emsp;签名和签名块文件将分别命名为`SIG.SF`和`SIG.DSA`，签名后的`JAR`文件`SignedApp.jar`将被放置在当前目录下。原始的未签名`JAR`文件将保持不变。此外，签名将使用`TSA`的公钥证书`testalias`加盖时间戳。



---



##### 其他信息

&emsp;&emsp;在线提供`JAR`签名和验证工具的完整参考页面：[安全工具概要](https://docs.oracle.com/javase/8/docs/technotes/guides/security/SecurityToolsSummary.html)

&emsp;&emsp;注意：当证书为自签名时，UNKNOWN 将显示为应用程序的发布者。欲了解更多信息，请参阅 "[运行 UNKNOWN 发布者的应用程序是否安全](https://www.java.com/en/download/help/self_signed.html)？