# ServletConfig

### ServletConfig作用

`ServletConfig`对象是`Servlet`的专属配置对象，每个`Servlet`都单独拥有一个`ServletConfig`对象，用来获取`web.xml`中的配置信息。

### ServletConfig使用

在`web.xml`中配置servlet配置信息

```xml
    <servlet>
        <servlet-name>ServletConfigServlet</servlet-name>
        <servlet-class>org.example.servlet.config.ServletConfigServlet</servlet-class>
        <init-param>
            <param-name>xxx</param-name>
            <param-value>aaa</param-value>
        </init-param>
        <init-param>
            <param-name>yyy</param-name>
            <param-value>bbb</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>ServletConfigServlet</servlet-name>
        <url-pattern>/servletConfigServlet</url-pattern>
    </servlet-mapping>
```



##### 获取`ServletConfig`对象

```java
        ServletConfig servletConfig = this.getServletConfig();
```



##### 获取`web.xml`中servlet的配置信息

```java
        String aaa = servletConfig.getInitParameter("xxx");
        Enumeration<String> initParameterNames = servletConfig.getInitParameterNames();
```

