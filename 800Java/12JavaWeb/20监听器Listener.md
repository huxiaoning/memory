# 监听器`Listener`

### 概念

`Servlet`监听器是`Servlet`规范中定义的一种特殊类，用于监听`ServletContext`、`HttpSession`和`ServletRequest`等域对象的创建与销毁事件，以及监听这些域对象中属性发生修改的事件。

### 监听对象

- Request
- Session
- Application

### 监听内容

- 创建、销毁
- 属性改变事件



```java
        ServletRequestListener,
        ServletRequestAttributeListener,
        HttpSessionListener,
        HttpSessionAttributeListener,
        ServletContextListener,
        ServletContextAttributeListener
```

### 相关接口

- Request

  - ServletRequestListener
    - `void requestDestroyed(ServletRequestEvent sre);`
    - `void requestInitialized(ServletRequestEvent sre);`

  - ServletRequestAttributeListener
    - `void attributeAdded(ServletRequestAttributeEvent srae);`
    - `void attributeRemoved(ServletRequestAttributeEvent srae);`
    - `void attributeReplaced(ServletRequestAttributeEvent srae);`

- Session

  - HttpSessionListener
    - `void sessionCreated(HttpSessionEvent se);`
    - `void sessionDestroyed(HttpSessionEvent se);`

  - HttpSessionAttributeListener
    - `void attributeAdded(HttpSessionBindingEvent event);`
    - `void attributeRemoved(HttpSessionBindingEvent event);`
    - `void attributeReplaced(HttpSessionBindingEvent event);`

- Application

  - ServletContextListener
    - `void contextInitialized(ServletContextEvent sce);`
    - `void contextDestroyed(ServletContextEvent sce);`

  - ServletContextAttributeListener
    - `void attributeAdded(ServletContextAttributeEvent event);`
    - `void attributeRemoved(ServletContextAttributeEvent event);`
    - `void attributeReplaced(ServletContextAttributeEvent event);`



### 请求监听器示例

```xml
    <listener><!-- web.xml -->
        <listener-class>org.example.servlet.listener.MyRequestListener</listener-class>
    </listener>
```

```java
import javax.servlet.*;
import javax.servlet.annotation.WebListener;
// @WebListener
public class MyRequestListener implements
        ServletRequestListener,
        ServletRequestAttributeListener {

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        ServletRequest servletRequest = sre.getServletRequest();
        ServletContext servletContext = sre.getServletContext();
        System.out.println("请求被创建");
    }

    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        ServletRequest servletRequest = sre.getServletRequest();
        ServletContext servletContext = sre.getServletContext();
        System.out.println("请求被销毁");
    }


    @Override
    public void attributeAdded(ServletRequestAttributeEvent srae) {
        ServletRequest servletRequest = srae.getServletRequest();
        ServletContext servletContext = srae.getServletContext();
        String name = srae.getName();
        Object value = srae.getValue();
        System.out.println("属性被添加:" + name + "-" + value);
    }

    @Override
    public void attributeRemoved(ServletRequestAttributeEvent srae) {
        ServletRequest servletRequest = srae.getServletRequest();
        ServletContext servletContext = srae.getServletContext();
        String name = srae.getName();
        Object value = srae.getValue();
        System.out.println("属性被移除:" + name + "-" + value);
    }

    @Override
    public void attributeReplaced(ServletRequestAttributeEvent srae) {
        ServletRequest servletRequest = srae.getServletRequest();
        ServletContext servletContext = srae.getServletContext();
        String name = srae.getName();
        Object value = srae.getValue(); // 替换前的旧值
        System.out.println("属性被替换:" + name + "-" + value);
    }
}
```



### 绘话监听器示例

```xml
    <listener><!-- web.xml -->
        <listener-class>org.example.servlet.listener.MySessionListener</listener-class>
    </listener>
```

```java
import javax.servlet.annotation.WebListener;
import javax.servlet.http.*;
// @WebListener
public class MySessionListener implements
        HttpSessionListener,
        HttpSessionAttributeListener {

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        HttpSession session = se.getSession();
        System.out.println("会话创建~" + session.getId());
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        HttpSession session = se.getSession();
        System.out.println("会话销毁~" + session.getId());
    }

    @Override
    public void attributeAdded(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("属性被添加:" + event.getName() + "-" + event.getValue());
    }

    @Override
    public void attributeRemoved(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        System.out.println("属性被移除:" + event.getName() + "-" + event.getValue());
    }

    @Override
    public void attributeReplaced(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        // value是替换前的旧值
        System.out.println("属性被替换:" + event.getName() + "-" + event.getValue());
    }
}
```

### 应用监听器示例

```xml
    <listener><!-- web.xml -->
        <listener-class>org.example.servlet.listener.MyApplicationListener</listener-class>
    </listener>
```

```java
import javax.servlet.*;
import javax.servlet.annotation.WebListener;
// @WebListener
public class MyApplicationListener implements ServletContextListener, ServletContextAttributeListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext servletContext = sce.getServletContext();
        System.out.println("应用创建");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        ServletContext servletContext = sce.getServletContext();
        System.out.println("应用销毁");
    }

    @Override
    public void attributeAdded(ServletContextAttributeEvent event) {
        ServletContext servletContext = event.getServletContext();
        System.out.println("属性被添加:" + event.getName() + "-" + event.getValue());
    }

    @Override
    public void attributeRemoved(ServletContextAttributeEvent event) {
        ServletContext servletContext = event.getServletContext();
        System.out.println("属性被移除:" + event.getName() + "-" + event.getValue());
    }

    @Override
    public void attributeReplaced(ServletContextAttributeEvent event) {
        ServletContext servletContext = event.getServletContext();
        // value是替换前的旧值
        System.out.println("属性被替换:" + event.getName() + "-" + event.getValue());
    }
}
```



# 监听器配置方式

- `web.xml`

  - ```xml
        <listener>
            <listener-class>org.example.servlet.listener.MyApplicationListener</listener-class>
        </listener>
    ```

- 注解(Servlet3.0及以上支持注解)

  - `@WebListener`

  - 看下注解源码

    - ```java
      package javax.servlet.annotation;
      
      import java.lang.annotation.Documented;
      import java.lang.annotation.ElementType;
      import java.lang.annotation.Retention;
      import java.lang.annotation.RetentionPolicy;
      import java.lang.annotation.Target;
      
      /**
       * This annotation is used to declare a WebListener.
       *
       * Any class annotated with WebListener must implement one or more of
       * the {@link javax.servlet.ServletContextListener}, 
       * {@link javax.servlet.ServletContextAttributeListener},
       * {@link javax.servlet.ServletRequestListener},
       * {@link javax.servlet.ServletRequestAttributeListener}, 
       * {@link javax.servlet.http.HttpSessionListener}, or
       * {@link javax.servlet.http.HttpSessionAttributeListener} interfaces.
       * 
       * @since Servlet 3.0
       */
      @Target({ElementType.TYPE})
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      public @interface WebListener {
          /**
           * Description of the listener
           */
          String value() default "";
      }
      ```

