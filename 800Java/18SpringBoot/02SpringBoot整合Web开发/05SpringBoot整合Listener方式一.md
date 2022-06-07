# SpringBoot整合Listener方式一

注解扫描。



- 编写Listener

  - ```java
    package com.example.bootdemo.listener;
    import javax.servlet.ServletContextEvent;
    import javax.servlet.ServletContextListener;
    import javax.servlet.annotation.WebListener;
    @WebListener
    public class FirstListener implements ServletContextListener {
        @Override
        public void contextInitialized(ServletContextEvent sce) {
            System.out.println("FirstListener 初始化。");
        }
        @Override
        public void contextDestroyed(ServletContextEvent sce) {
            ServletContextListener.super.contextDestroyed(sce);
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

  - 