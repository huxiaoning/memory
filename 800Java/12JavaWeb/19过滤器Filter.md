# 过滤器`Filter`

### 第一个过滤器

- 编写一个待拦截的`Servlet`

  - ```java
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    
    @WebServlet(name = "filterServlet", urlPatterns = {"/filterServlet"})
    public class FilterServlet extends HttpServlet {
    
        @Override
        protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            req.setCharacterEncoding("utf-8");
            resp.setContentType("text/html;charset=utf-8");
    
            resp.getWriter().write("一些无关紧要的信息");
        }
    }
    ```

- 编写过滤器

  - ```java
    import javax.servlet.*;
    import java.io.IOException;
    // @WebFilter(filterName = "myFilter", urlPatterns = {"/*"})
    public class MyFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            System.out.println("MyFilter init");
        }
    
        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            System.out.println("MyFilter before doFilter");
            chain.doFilter(request, response); // 放行，后续拦截
            System.out.println("MyFilter after doFilter");
        }
    
        @Override
        public void destroy() {
            System.out.println("MyFilter destroy");
        }
    }
    ```

- 在`web.xml`中配置过滤器

  - ```xml
        <filter>
            <filter-name>myFilter</filter-name>
            <filter-class>org.example.servlet.filter.MyFilter</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>myFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping>
    ```

  - 当然也可以使用注解配置(web3.0以上支持)`@WebFilter(filterName = "myFilter", urlPatterns = {"/*"})`

  - 也可以根据`Servlet`的名字过滤

    - ```xml
          <filter>
              <filter-name>myFilter</filter-name>
              <filter-class>org.example.servlet.filter.MyFilter</filter-class>
          </filter>
          <filter-mapping>
              <filter-name>myFilter</filter-name>
              <servlet-name>filterServlet</servlet-name>
          </filter-mapping>
      ```

    - ```java
      @WebFilter(filterName = "myFilter", servletNames = {"filterServlet"})
      ```



### WebFilter注解源码(看下都可以配置什么)，xml类似

```java
package javax.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import javax.servlet.DispatcherType;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebFilter {

    /**
     * The description of the filter
     */
    String description() default "";
    
    /**
     * The display name of the filter
     */
    String displayName() default "";
    
    /**
     * The init parameters of the filter
     */
    WebInitParam[] initParams() default {};
    
    /**
     * The name of the filter
     */
    String filterName() default "";
    
    /**
     * The small-icon of the filter
     */
    String smallIcon() default "";

    /**
     * The large-icon of the filter
     */
    String largeIcon() default "";

    /**
     * The names of the servlets to which the filter applies.
     */
    String[] servletNames() default {};
    
    /**
     * The URL patterns to which the filter applies
     */
    String[] value() default {};

    /**
     * The URL patterns to which the filter applies
     */
    String[] urlPatterns() default {};

    /**
     * The dispatcher types to which the filter applies
     */
    DispatcherType[] dispatcherTypes() default {DispatcherType.REQUEST};
    
    /**
     * Declares whether the filter supports asynchronous operation mode.
     *
     * @see javax.servlet.ServletRequest#startAsync
     * @see javax.servlet.ServletRequest#startAsync(ServletRequest,
     * ServletResponse)
     */
    boolean asyncSupported() default false;
}
```



### 实现的过滤器接口API说明

- `void init(FilterConfig filterConfig) throws ServletException;`
  - 服务器启动时执行一次。资源初始化
- `void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException;`
  - 过滤请求的方法，在此方法中可以对资源实现管理
  - 需要手动判断请求是否放行：`chain.doFilter(request, response);`
  - 
- `void destroy();`
  - 服务器关闭时执行。

### 过滤器的生命周期

服务器启动到服务器关闭。

### 多个过滤器的执行顺序

- `web.xml`配置方式
  - 按`filter-mapping`的配置顺序
- `@WebFilter`注解方式
  - 注解并无控制方式，按过滤器类名的自然排序顺序,如:`com.a.Filter1`优先于`com.b.Filter2`

**也许`J2EE`规范认为过滤器的执行顺序并不重要**。

过滤器链中最后执行的是`Servlet`。

### url-patten拦截地址说明

- `/*`
  - 表示过滤所有请求
- `*.do`
  - 过滤所有以.do结尾的请求。一般是用来进行模块拦截处理。
- `/xxx`
  - 表示过滤指定url的请求。针对某个servlet的请求进行过滤，保护servlet。



### 过滤器的执行逻辑

浏览器发起请求到服务器，服务器接收到请求后，根据URI信息在web.xml中找到对应的过滤器执行doFilter方法，

该方法对此次请求进行处理后如果符合要求则放行，放行后如果还有符合要求的过滤则继续进行过滤，

找到执行对应的servlet进行请求处理，servlet对请求处理完毕后，也就service方法结束了。

还需继续返回相应的doFilter方法继续执行。



### 过滤器案例

- 统一编码格式设置

  - ```java
    import javax.servlet.*;
    import javax.servlet.annotation.WebFilter;
    import java.io.IOException;
    
    @WebFilter(filterName = "encodingFilter", urlPatterns = {"/*"})
    public class EncodingFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {}
    
        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            request.setCharacterEncoding("utf-8");
            response.setContentType("text/html;charset=utf-8");
    
            chain.doFilter(request, response);
        }
    
        @Override
        public void destroy() {}
    }
    ```

  - 这样所有的`Servlet`中都不用写这两句编码格式设置的代码了。

- session过期处理

  - ```java
    import org.example.servlet.domain.User;
    
    import javax.servlet.*;
    import javax.servlet.annotation.WebFilter;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import javax.servlet.http.HttpSession;
    import java.io.IOException;
    
    @WebFilter(filterName = "sessionFilter", urlPatterns = {"/*"})
    public class SessionFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {}
    
        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            HttpServletRequest req = (HttpServletRequest) request;
            HttpSession session = req.getSession();
    
            User user = (User) session.getAttribute("user");
            if (user == null) {
                HttpServletResponse resp = (HttpServletResponse) response;
                resp.sendRedirect("/login.jsp");
                return;
            }
            
            chain.doFilter(request, response);
        }
    
        @Override
        public void destroy() {}
    }
    ```

  - 

- 权限管理

- 资源管理

  - 统一水印
  - 和谐词汇



### 可以把统一编码格式和session管理的Filter合到一块

```java
import org.example.servlet.domain.User;
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebFilter(filterName = "preFilter", urlPatterns = {"/*"})
public class PreFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {}

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) request;
        HttpServletResponse resp = (HttpServletResponse) response;

        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        
        HttpSession session = req.getSession();
        User user = (User) session.getAttribute("user");
        if (user == null) {
            resp.sendRedirect("/login.jsp");
            return;
        }
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {}
}
```

