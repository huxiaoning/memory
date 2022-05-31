# SpringBoot整合Servlet方式二

@Bean方法。



- 1 编写Servlet

  - ```java
    package com.example.bootdemo.servlet;
    
    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    
    public class SecondServlet extends HttpServlet {
    
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            doPost(req, resp);
        }
    
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("访问了SecondServlet");
        }
    }
    ```

- 2 编写启动类

  - ```java
    package com.example.bootdemo;
    import com.example.bootdemo.servlet.SecondServlet;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
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
    }
    ```

