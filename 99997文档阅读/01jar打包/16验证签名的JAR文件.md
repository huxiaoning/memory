# 验证签名的JAR文件

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/verify.html)



---



&emsp;&emsp;通常，验证已签名的 `JAR` 文件是 `Java™ Runtime Environment` 的责任。浏览器将验证其下载的签名小程序。使用解释器的 `-jar` 选项调用的签名应用程序将由运行时环境进行验证。

&emsp;&emsp;不过，您可以使用`jarsigner`工具自行验证已签名的`JAR`文件。例如，您可能需要这样做来测试您准备的已签名`JAR`文件。

&emsp;&emsp;验证已签名`JAR`文件的基本命令是:

```bash
$ jarsigner -verify jar-file
```

&emsp;&emsp;该命令将验证`JAR`文件的签名，并确保归档文件中的文件在签名后没有更改。如果验证成功，您将看到以下消息：

```bash
jar verified.
```

&emsp;&emsp;如果您尝试验证未签名的 `JAR` 文件，则会出现以下消息：

```bash
jar is unsigned. (signatures missing or not parsable)
```

&emsp;&emsp;如果验证失败，将显示适当的消息。例如，如果`JAR`文件的内容在`JAR`文件被签署后发生了更改，则如果您尝试验证该文件，将显示与下面类似的消息：

```bash
jarsigner: java.lang.SecurityException: invalid SHA1 
signature file digest for test/classes/Manifest.class
```

> 注意：如果已签名的 `JAR` 文件使用了在 `java.home/lib/security/java.security` 文件（其中 `java.home` 是您安装 `JRE` 的目录）中的 `jdk.jar.disabledAlgorithms` 安全属性中指定的任何算法，则 `JDK` 会将已签名的 `JAR` 文件视为未签名。