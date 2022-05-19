# `Servlet`生命周期

- 从第一次调用，到服务器关闭。

- 如果在`web.xml`中配置了`load-on-startup`(`>=0`即可)则是从服务器开启到服务器关闭。



```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>org.example.servlet.MyServlet</servlet-class>
        <load-on-startup>1</load-on-startup> <!-- Tomcat服务器启动时创建Servlet对象实例,数字表示加载顺序 -->
    </servlet>
    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/myServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

或者

```java
@WebServlet(name = "/a", loadOnStartup = 1)
public class MyServlet extends HttpServlet {
    
}
```



### `Servlet`接口

```java
package javax.servlet;
import java.io.IOException;
public interface Servlet {
    public void init(ServletConfig config) throws ServletException;
    public ServletConfig getServletConfig();
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;
    public String getServletInfo()
    public void destroy();
}
```



### 生命周期方法说明

```java
package org.example.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

// @WebServlet(name = "/servletLife1", urlPatterns = {"/servletLife1"}, loadOnStartup = 1)
public class ServletLife extends HttpServlet {

    public ServletLife() {
        // 默认情况下，第一次被访问时，Servlet被创建，然后执行init方法；
        // 可以配置执行Servlet的创建时机； loadOnStartup | <load-on-startup>1</load-on-startup> 指定Tomcat启动时执行初始化
        System.out.println("servlet构造完成。");
    }

    @Override
    public void init() throws ServletException {
        System.out.println("servlet初始化完成。");
    }

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 执行service方法，执行多次,刷新一次浏览器页面就会执行一次
        resp.getWriter().write("servlet life。");
        System.out.println("servlet life。");
    }

    @Override
    public void destroy() {
        // 当Servlet服务器正常关闭时，执行destroy方法，只执行一次
        System.out.println("servlet销毁。");
    }
}
```



### `doGet`和`doPost`

正常情况下不需要重写`service`方法,`HttpServlet`类的`service`方法已经做了方法分发：

```java
    protected void service(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String method = req.getMethod();

        if (method.equals(METHOD_GET)) {
            long lastModified = getLastModified(req);
            if (lastModified == -1) {
                // servlet doesn't support if-modified-since, no reason
                // to go through further expensive logic
                doGet(req, resp);
            } else {
                long ifModifiedSince = req.getDateHeader(HEADER_IFMODSINCE);
                if (ifModifiedSince < lastModified) {
                    // If the servlet mod time is later, call doGet()
                    // Round down to the nearest second for a proper compare
                    // A ifModifiedSince of -1 will always be less
                    maybeSetLastModified(resp, lastModified);
                    doGet(req, resp);
                } else {
                    resp.setStatus(HttpServletResponse.SC_NOT_MODIFIED);
                }
            }

        } else if (method.equals(METHOD_HEAD)) {
            long lastModified = getLastModified(req);
            maybeSetLastModified(resp, lastModified);
            doHead(req, resp);

        } else if (method.equals(METHOD_POST)) {
            doPost(req, resp);
            
        } else if (method.equals(METHOD_PUT)) {
            doPut(req, resp);
            
        } else if (method.equals(METHOD_DELETE)) {
            doDelete(req, resp);
            
        } else if (method.equals(METHOD_OPTIONS)) {
            doOptions(req,resp);
            
        } else if (method.equals(METHOD_TRACE)) {
            doTrace(req,resp);
            
        } else {
            //
            // Note that this means NO servlet supports whatever
            // method was requested, anywhere on this server.
            //

            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[1];
            errArgs[0] = method;
            errMsg = MessageFormat.format(errMsg, errArgs);
            
            resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
        }
    }    
```

一般`servlet`开发需要复写`doGet`或`doPost`方法。



### 示例

```java
@WebServlet("/method")
public class ServletMethod extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("service");
        super.service(req, resp);
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost");
    }
}
```

`method.jsp`

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" pageEncoding="utf-8" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="method" method="post">
    用户名:<input type="text" name="uname" value=""><br>
    密码: <input type="password" name="pwd" value=""><br>
    <input type="submit" value="登录">
</form>
</body>
</html>
```

##### 访问说明

- http://localhost:8080/web-demo/method.jsp 访问的是`method.jsp`页面
- http://localhost:8080/web-demo/method 访问的是`ServletMethod`的`doGet`|`doPost`即`service`方法



