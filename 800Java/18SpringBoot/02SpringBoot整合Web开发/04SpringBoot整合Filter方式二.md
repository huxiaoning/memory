# SpringBoot整合Filter方式二

@Bean方法。



- 编写Filter

  - ```java
    package com.example.bootdemo.filter;
    import javax.servlet.*;
    import java.io.IOException;
    public class SecondFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            Filter.super.init(filterConfig);
        }
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            System.out.println("进入SecondFilter");
            filterChain.doFilter(servletRequest, servletResponse);
            System.out.println("离开SecondFilter");
        }
        @Override
        public void destroy() {
            Filter.super.destroy();
        }
    }
    ```

- 编写启动类

  - ```java
    package com.example.bootdemo;
    import com.example.bootdemo.filter.SecondFilter;
    import com.example.bootdemo.servlet.SecondServlet;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.web.servlet.FilterRegistrationBean;
    import org.springframework.boot.web.servlet.ServletRegistrationBean;
    import org.springframework.context.annotation.Bean;
    @SpringBootApplication
    public class BootDemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(BootDemoApplication.class, args);
        }
        @Bean
        public ServletRegistrationBean<SecondServlet> getServletRegistrationBean() {
            ServletRegistrationBean<SecondServlet> bean = new ServletRegistrationBean(new SecondServlet());
            bean.addUrlMappings("/SecondServlet");
            return bean;
        }
        @Bean
        public FilterRegistrationBean<SecondFilter> getFilterRegistrationBean() {
            FilterRegistrationBean<SecondFilter> bean = new FilterRegistrationBean(new SecondFilter());
            bean.addUrlPatterns("/*");
            return bean;
        }
    }
    ```

