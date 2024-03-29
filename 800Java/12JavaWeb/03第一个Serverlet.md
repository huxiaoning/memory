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
    <welcome-file-list> <!-- 可以不配置这个 -->
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
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



### 源码目录结构

![image-20220522151318303](https://raw.githubusercontent.com/huxiaoning/img/master/20220522151318.png)

maven中的webapp目录等同于，eclipse中的web-content目录和myeclipse中的web-root目录

这个目录下的所以内容会部署到Tomcat中的webapps目录下的web-demo(虚拟项目名称)目录下。

当然这里面没有class等编译后的文件，部署后会有class文件放在WEB-INF目录下。

### 最终web项目目录结构

![image-20220519145823911](https://raw.githubusercontent.com/huxiaoning/img/master/20220519145823.png)

![image-20220519145909957](https://raw.githubusercontent.com/huxiaoning/img/master/20220519145910.png)

访问地址

- `http://localhost:8080/web-demo/myServlet`
  - 相当于 `http://localhost:8080/web-demo/myServlet/index.jsp`
- `http://localhost:8080/web-demo/a`  (这种不需要配置web.xml)

### Servlet是单例的吗？

是。(只需要把MyServlet的无参构造函数写出来，在里面添加一句打印语句，即可验证)

Servlet被创建的时机被一次被访问时。也就是延迟加载。相当于懒汉模式。





### WebServlet注解源码(看下都可以配置什么)，xml类似

```java
package javax.servlet.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Target;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Retention;
import java.lang.annotation.Documented;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebServlet {
    
    /**
     * The name of the servlet
     */
    String name() default "";
    
    /**
     * The URL patterns of the servlet
     */
    String[] value() default {};

    /**
     * The URL patterns of the servlet
     */
    String[] urlPatterns() default {};
    
    /**
     * The load-on-startup order of the servlet 
     */
    int loadOnStartup() default -1;
    
    /**
     * The init parameters of the servlet
     */
    WebInitParam [] initParams() default {};
    
    /**
     * Declares whether the servlet supports asynchronous operation mode.
     *
     * @see javax.servlet.ServletRequest#startAsync
     * @see javax.servlet.ServletRequest#startAsync(ServletRequest,
     * ServletResponse)
     */
    boolean asyncSupported() default false;
    
    /**
     * The small-icon of the servlet
     */
    String smallIcon() default "";

     /**
      * The large-icon of the servlet
      */
    String largeIcon() default "";

    /**
     * The description of the servlet
     */
    String description() default "";

    /**
     * The display name of the servlet
     */
    String displayName() default "";
}
```

