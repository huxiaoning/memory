# 签署JAR文件

[原文地址](https://docs.oracle.com/javase/tutorial/deployment/jar/signing.html)



---



&emsp;&emsp;你使用`JAR`签名和验证工具来签署`JAR`文件，并在签名上盖上时间戳。你通过使用`jarsigner`命令来调用`JAR`签名和验证工具，所以我们会把它简称为 "`Jarsigner`"。

&emsp;&emsp;要签署一个`JAR`文件，你必须先有一个私钥。私钥及其相关的公钥证书被存储在有密码保护的数据库中，称为钥匙库。一个钥匙库可以保存许多潜在签名者的钥匙。钥匙库中的每一把钥匙都可以通过别名来识别，而别名通常是拥有该钥匙的签名人的名字。例如，属于`Rita Jones`的钥匙可能有一个别名 "`rita`"。