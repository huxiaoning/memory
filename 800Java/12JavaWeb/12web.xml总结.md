# web.xml总结

### `web.xml`文件位置

![image-20220520230227915](https://raw.githubusercontent.com/huxiaoning/img/master/20220520230236.png)

- 项目中
- Tomcat服务器conf目录下

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>

    <!-- 配置全局数据 -->
    <context-param> <!-- 全局初始化参数 -->
        <param-name>xxx</param-name>
        <param-value>aaa</param-value>
    </context-param>
    <context-param> <!-- 全局初始化参数 -->
        <param-name>yyy</param-name>
        <param-value>bbb</param-value>
    </context-param>

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>org.example.servlet.MyServlet</servlet-class>
        <load-on-startup>-2</load-on-startup> <!-- Tomcat服务器启动时创建Servlet对象实例 -->
    </servlet>
    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/myServlet</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>ServletLife</servlet-name>
        <servlet-class>org.example.servlet.ServletLife</servlet-class>
        <load-on-startup>1</load-on-startup> <!-- Tomcat服务器启动时创建Servlet对象实例 -->
    </servlet>
    <servlet-mapping>
        <servlet-name>ServletLife</servlet-name>
        <url-pattern>/servletLife</url-pattern>
    </servlet-mapping>

    <session-config> <!-- 配置所有session的过期时间为30分钟 -->
        <session-timeout>30</session-timeout>
    </session-config>


    <servlet>
        <servlet-name>ServletConfigServlet</servlet-name>
        <servlet-class>org.example.servlet.config.ServletConfigServlet</servlet-class>
        <init-param> <!-- servlet专有初始化参数 -->
            <param-name>xxx</param-name>
            <param-value>aaa</param-value>
        </init-param>
        <init-param> <!-- servlet专有初始化参数 -->
            <param-name>yyy</param-name>
            <param-value>bbb</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>ServletConfigServlet</servlet-name>
        <url-pattern>/servletConfigServlet</url-pattern>
    </servlet-mapping>
</web-app>

```



### `web.xml`配置内容如下：

- 全局上下文<context-param>

- Servlet配置
  - load-on-startup
  - <init-param>
- 过滤器配置
- 监听器配置

### `web.xml`加载顺序

- `ServletContext`
  - `context-param`
- `listener`
- `filter`
- `servlet`

加载按上述顺序加载，但配置顺序无限制。

加载时机为服务器启动时。



## `server.xml`

```xml
<Server>
	<Service name="Catalina">
        <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" useBodyEncodingForURI="true"/>
        <Connector></Connector>
        <Engine name="Catalina" defaultHost="localhost">
        	<Host name="localhost"  appBase="webapps"
            	unpackWARs="true" autoDeploy="true">
            	<Context/>
            </Host>
        </Engine>
    </Service>
</Server>
```

### 热部署（试了没什么用）

```xml
<Context path="/web-demo" reloadable="true" docBase="C:\developerInstall\IdeaWorkspace\web-demo\target\web-demo"/>
```

