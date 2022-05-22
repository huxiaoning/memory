# JSP

全称`Java Server Pages`。

本质上还是`Servlet`。

#### 原理

Tomcat中conf目录下的web.xml文件中有关于jsp的Servlet配置:

```xml
    <servlet>
        <servlet-name>jsp</servlet-name>
        <servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
        <init-param>
            <param-name>fork</param-name>
            <param-value>false</param-value>
        </init-param>
        <init-param>
            <param-name>xpoweredBy</param-name>
            <param-value>false</param-value>
        </init-param>
        <load-on-startup>3</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>jsp</servlet-name>
        <url-pattern>*.jsp</url-pattern>
        <url-pattern>*.jspx</url-pattern>
    </servlet-mapping>
```

若当前访问地址为a.jsp，Tomcat会选择JspServlet来处理这个请求，JspServlet干了一件事情：

那就是把a.jsp里面的内容转化成下面这样：

```java
        resp.getWriter().write("<html>");
        resp.getWriter().write("<head>");
		// ...
        resp.getWriter().write("</head>");
        resp.getWriter().write("<body>");
		// ...
        resp.getWriter().write("</body>");
        resp.getWriter().write("</html>");
```

其详细过程是把a.jsp转化成一个a_jsp.java文件，再编译成a_jsp.class，再请求转发给这个a_jsp.class





![image-20220521104359860](https://raw.githubusercontent.com/huxiaoning/img/master/20220521104407.png)



### Idea中查看jsp转译的Servlet文件

idea启动时控制台一行输出如下：

```
Using CATALINA_BASE:   "C:\Users\%USERNAME%\AppData\Local\JetBrains\IntelliJIdea2021.2\tomcat\064f584c-f937-4fe0-8b45-d21e5ba13057"
```

有这个目录下的如下位置

`\work\Catalina\localhost\web-demo\org\apache\jsp\`

### 示例`a.jsp`

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    // /web-demo
    String path = request.getContextPath();
    // http://localhost:8080/web-demo/
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path + "/";
%>
<html>
<head>
    <base href="<%=basePath%>">
    <title>Title</title>
</head>
    <body>
        <%=path%>
        <br>
        <%=basePath%>
    </body>
</html>
```



### 三种注释

- 前端语言注释
  - 会被转译，也会被发送，但是不会被浏览器执行
    - HTML - `<!--  -->`
    - JS - `//`
    - CSS - 
- Java注释
  - 会被转译，但是不会被Servlet执行。
- Jsp注释
  - 不会被转译
    - `<%--  --%>`

