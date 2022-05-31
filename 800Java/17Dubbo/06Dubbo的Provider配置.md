# Dubbo的Provider配置

Maven多模块项目规划说明

- dubbo-parent
  - dubbo-interface
  - dubbo-provider
  - dubbo-consumer



### dubbo-interface

```java
package org.example.dubbo.service;
public interface DemoService {
    String sayHello(String name);
}

```

### dubbo-provider

- `pom.xml`

  - ```xml
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
            <dependency><!-- 接口模块 -->
                <groupId>org.example</groupId>
                <artifactId>dubbo-interface</artifactId>
                <version>1.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo</artifactId>
                <version>2.6.12</version>
                <exclusions>
                    <exclusion><!-- 去掉老版本的spring依赖 -->
                        <groupId>org.springframework</groupId>
                        <artifactId>spring-context</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency><!-- 加入新版本的spring依赖 -->
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>5.3.20</version>
            </dependency>
            <dependency>
                <groupId>io.netty</groupId>
                <artifactId>netty-all</artifactId>
                <version>4.1.77.Final</version>
            </dependency>
            <dependency>
                <groupId>org.apache.curator</groupId>
                <artifactId>curator-framework</artifactId>
                <version>5.2.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.curator</groupId>
                <artifactId>curator-recipes</artifactId>
                <version>5.2.1</version>
            </dependency>
    ```

- `src/main/resources/META-INF/spring/applicationContext-dubbo.xml`

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    
        <dubbo:application name="dubbo-provider"/>
        <dubbo:registry address="127.0.0.1:2181" protocol="zookeeper"></dubbo:registry>
        <dubbo:protocol name="dubbo" port="20888"></dubbo:protocol>
    
        <dubbo:service interface="org.example.dubbo.service.DemoService" ref="demoServiceImpl"></dubbo:service>
        <bean id="demoServiceImpl" class="org.example.dubbo.service.impl.DemoServiceImpl"></bean>
    
        <!--3
        可以在Service的实现类上添加注解
        @com.alibaba.dubbo.config.annotation.Service
        来替换掉上面两行配置
        但这种方式与声明式事务冲突
        <dubbo:annotation package="org.example.service.impl"/>
        -->
    </beans>
    ```

- `DubboProviderApp.java`

  - 自实现

    - ```java
          public static void main(String[] args) throws IOException {
              ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("META-INF/spring/applicationContext-dubbo.xml");
              // 启动spring容器的同时，也启动了dubbo容器
              context.start();
              System.out.println("启动成功");
              System.in.read(); // 阻止应用程序自动关闭
          }
      ```

  - 官方推荐

    - ```java
      import com.alibaba.dubbo.container.Main;
      public class DubboProviderApp {
          public static void main(String[] args) {
              // 要求配置文件必须放在/META-INF/spring/*.xml
              Main.main(args);
          }
      }
      ```

    - ```
      log4j:WARN No appenders could be found for logger (com.alibaba.dubbo.common.logger.LoggerFactory).
      log4j:WARN Please initialize the log4j system properly.
      log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
      SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
      SLF4J: Defaulting to no-operation (NOP) logger implementation
      SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
      SLF4J: Failed to load class "org.slf4j.impl.StaticMDCBinder".
      SLF4J: Defaulting to no-operation MDCAdapter implementation.
      SLF4J: See http://www.slf4j.org/codes.html#no_static_mdc_binder for further details.
      [2022-05-31 10:01:42] Dubbo service server started!
      ```

