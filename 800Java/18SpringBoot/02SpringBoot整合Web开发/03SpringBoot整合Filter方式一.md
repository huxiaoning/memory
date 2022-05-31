# SpringBoot整合Filter方式一

注解扫描。



- 编写Filter

  - ```java
    package com.example.bootdemo.filter;
    import javax.servlet.*;
    import javax.servlet.annotation.WebFilter;
    import java.io.IOException;
    @WebFilter(filterName = "FirstFilter", urlPatterns = {"/*"})
    public class FirstFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            Filter.super.init(filterConfig);
        }
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            System.out.println("进入Filter");
            filterChain.doFilter(servletRequest, servletResponse);
            System.out.println("离开Filter");
        }
        @Override
        public void destroy() {
            Filter.super.destroy();
        }
    }
    
    ```

- 启动类添加注解`@ServletComponentScan`

  - ```java
    package com.example.bootdemo;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.web.servlet.ServletComponentScan;
    
    @SpringBootApplication
    @ServletComponentScan
    public class BootDemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(BootDemoApplication.class, args);
        }
    }
    ```

  - 注意：没有@FilterComponentScan。

  - @ServletComponentScan会扫描@WebFilter，并将该类实例化。

