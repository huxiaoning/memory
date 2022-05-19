# Tomcat

### 目录结构

- \bin
  - 存放启动和关闭Tomcat的可执行文件
- \conf
  - 存放Tomcat的配置文件
- \lib
  - 存放库文件
- \logs
  - 存放日志文件
- \temp
  - 存放临时文件
- \webapps
  - 存放web 应用
- \work
  - 存放JSP转换后的Servlet文件



### 修改Tomcat端口

`$ vim conf/server.xml`

```xml
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```



### Idea配置Tomcat

![image-20220519141823444](C:\Users\Jett\AppData\Roaming\Typora\typora-user-images\image-20220519141823444.png)

#### 启动web项目

![image-20220519141949218](https://raw.githubusercontent.com/huxiaoning/img/master/20220519141949.png)



