# jar打包

[文档地址](https://docs.oracle.com/javase/tutorial/deployment/jar/index.html)



需要下载[jdk demos and samples](https://www.oracle.com/java/technologies/java-archive-misc-downloads.html#jdk8demos)

```
$ tar -zxvf jdk-8-linux-x64-demos.tar.gz
$ ls
jdk1.8.0_341  jdk-8-linux-x64-demos.tar.gz
```

后续的演示都在`jdk1.8.0_341`目录中的某个子目录下。





---



<center>常见的JAR文件操作</center>

| 操作                                                         | 命令                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 创建一个`JAR`文件                                            | `jar cf jar-file input-file(s)`                              |
| 查看`JAR`文件的内容                                          | `jar tf jar-file`                                            |
| 提取一个`JAR`文件的内容                                      | `jar xf jar-file`                                            |
| 从一个`JAR`文件中提取特定的文件                              | `jar xf jar-file archived-file(s)`                           |
| 运行一个打包成`JAR`文件的应用程序（需要`Main-class`清单头）。 | `java -jar app.jar`                                          |
| 调用打包成`JAR`文件的小程序                                  | <applet code=AppletClassName.class         archive="JarFileName.jar"         width=width height=height> </applet> |



---



