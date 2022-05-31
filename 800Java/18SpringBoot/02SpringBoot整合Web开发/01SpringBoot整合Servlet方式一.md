# SpringBoot整合Servlet方式一

注解扫描。



- 1 编写Servlet

  - ```java
    package com.example.bootdemo.servlet;
    
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;
    
    @WebServlet(name = "FirstServlet", urlPatterns = {"/FirstServlet"})
    public class FirstServlet extends HttpServlet {
    
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            doPost(req, resp);
        }
    
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("访问了FirstServlet");
        }
    }
    
    ```

- 2 启动类添加注解`@ServletComponentScan`

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

  - 在SpringBoot启动时会扫描@WebServlet，并将该类实例化。

