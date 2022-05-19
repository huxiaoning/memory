# 第一个Serverlet



### 用Idea新建一个maven web项目

maven-archetype-webapp

```xml
    <groupId>org.example</groupId>
    <artifactId>web-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
```



### maven依赖

```xml
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
```

### 编写MyServlet

```java
package org.example.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.concurrent.atomic.AtomicLong;

@WebServlet("/a") // 可以不用配置web.xml web 3.0.1 开始支持
public class MyServlet extends HttpServlet {

    private static final AtomicLong num = new AtomicLong(0);

    public MyServlet() {
        num.incrementAndGet();
    }

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("this is my first servlet.");
        System.out.println("this is my first servlet.");
        System.out.println(num.get()); // 无论请求几次，这里都只会打印1，因为servlet是单例的。
    }
}

```

### 在web.xml中配置myServlet

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <servlet>
        <servlet-name>MyServlet</servlet-name>
        <servlet-class>org.example.servlet.MyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>MyServlet</servlet-name>
        <url-pattern>/myServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

### 部署myServlet到Tomcat

##### 手动部署方式1

```bash
$ cp -r target/web-demo $CATALINA_HOME/webapps/
```

##### 手动部署方式2

```bash
$ cp target/web-demo.war $CATALINA_HOME/webapps/
```



### 最终web项目目录结构

![image-20220519145823911](https://raw.githubusercontent.com/huxiaoning/img/master/20220519145823.png)

![image-20220519145909957](https://raw.githubusercontent.com/huxiaoning/img/master/20220519145910.png)

访问地址

- `http://localhost:8080/web-demo/myServlet`
- `http://localhost:8080/web-demo/a`  (这种不需要配置web.xml)

### Servlet是单例的吗？

是。(只需要把MyServlet的无参构造函数写出来，在里面添加一句打印语句，即可验证)

Servlet被创建的时机被一次被访问时。也就是延迟加载。相当于懒汉模式。



