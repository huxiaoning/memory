# Maven常用命令

```java
# 下载源码
$ mvn dependency:sources
# 下载文档
$ mvn dependency:resolve -Dclassifier=javadoc
```





```java
$ mvn clean package -Dmaven.test.skip=true
$ mvn clean install -Dmaven.test.skip=true
```

