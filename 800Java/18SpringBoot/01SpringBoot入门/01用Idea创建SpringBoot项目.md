# 用Idea创建SpringBoot项目

- 1 创建Spring-Boot项目
  - ![image-20220531231017870](https://raw.githubusercontent.com/huxiaoning/img/master/20220531231017.png)

- 2 选择需要的模块，这里选择一个web模块
  - ![image-20220531231049359](https://raw.githubusercontent.com/huxiaoning/img/master/20220531231049.png)

- 完成。



### 生成的工程长什么样？

- 项目结构

  - ![image-20220531231318978](https://raw.githubusercontent.com/huxiaoning/img/master/20220531231319.png)

- `pom.xml`

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.7.0</version>
            <relativePath/> <!-- lookup parent from repository -->
        </parent>
        <groupId>com.example</groupId>
        <artifactId>boot-demo</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <name>boot-demo</name>
        <description>boot-demo</description>
        <properties>
            <java.version>1.8</java.version>
        </properties>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
    
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>
            </plugins>
        </build>
    
    </project>
    ```

- `com.example.bootdemo.BootDemoApplication.java`

  - ```java
    package com.example.bootdemo;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    @SpringBootApplication
    public class BootDemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(BootDemoApplication.class, args);
        }
    }
    ```

- `com.example.bootdemo.BootDemoApplicationTests.java`

  - ```java
    package com.example.bootdemo;
    import org.junit.jupiter.api.Test;
    import org.springframework.boot.test.context.SpringBootTest;
    @SpringBootTest
    class BootDemoApplicationTests {
        @Test
        void contextLoads() {
        }
    }
    ```





### 也可以用Spring提供的服务来生成这个项目的骨架

[地址](https://start.spring.io/)







### 创建一个Hello World控制器

```java
package com.example.bootdemo.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import java.util.HashMap;
import java.util.Map;
@Controller
public class HelloController {
    @RequestMapping("/hello")
    @ResponseBody
    public Map<String, Object> hello() {
        Map<String, Object> map = new HashMap<>();
        map.put("hello", "world");
        return map;
    }
}
```

