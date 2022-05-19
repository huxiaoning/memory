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
        <load-on-startup>1</load-on-startup> <!-- Tomcat服务器启动时创建Servlet对象实例 -->
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

