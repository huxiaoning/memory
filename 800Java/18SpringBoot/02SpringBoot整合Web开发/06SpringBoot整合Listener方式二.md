# SpringBoot整合Listener方式二

@Bean方法。



- 编写Listener

  - ```java
    package com.example.bootdemo.listener;
    import javax.servlet.ServletContextEvent;
    import javax.servlet.ServletContextListener;
    public class SecondListener implements ServletContextListener {
        @Override
        public void contextInitialized(ServletContextEvent sce) {
            System.out.println("SecondListener 初始化。");
        }
        @Override
        public void contextDestroyed(ServletContextEvent sce) {
        }
    }
    ```

- 编写启动类

  - ```java
    package com.example.bootdemo;
    import com.example.bootdemo.filter.SecondFilter;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.web.servlet.FilterRegistrationBean;
    import org.springframework.context.annotation.Bean;
    @SpringBootApplication
    public class BootDemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(BootDemoApplication.class, args);
        }
        @Bean
        public FilterRegistrationBean<SecondFilter> getFilterRegistrationBean() {
            FilterRegistrationBean<SecondFilter> bean = new FilterRegistrationBean(new SecondFilter());
            return bean;
        }
    }
    ```

