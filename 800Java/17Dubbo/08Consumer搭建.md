# Consumer搭建

实现工作中需要创建web模块，但仅仅为了测试，创建jar类型项目也是可以的。



- `pom.xml` 与服务提供者一致(可以考虑抽取到`dubbo-interface`模块)

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
                    <exclusion>
                        <groupId>org.springframework</groupId>
                        <artifactId>spring-context</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency>
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

- `src/main/resources/applicationContext-dubbo.xml`

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    
        <dubbo:application name="dubbo-consumer"/>
        <dubbo:registry address="127.0.0.1:2181" protocol="zookeeper"></dubbo:registry>
        <!-- 配置注解扫描 -->
        <dubbo:annotation package="org.example.service.impl"/>
    
        <bean id="testService" class="org.example.service.impl.TestServiceImpl"></bean>
    </beans>
    ```
  
- 相关测试代码

  - ```java
    package org.example.service;
    public interface TestService {
        void test();
    }
    ```
    
  - ```java
    package org.example.service.impl;
    
    import com.alibaba.dubbo.config.annotation.Reference;
    import org.example.dubbo.service.DemoService;
    import org.example.service.TestService;
    public class TestServiceImpl implements TestService {
        // 注意这个注解是dubbo包提供的,以前这里是引入Mapper
        @Reference
        private DemoService demoService;
        @Override
        public void test() {
            String ret = demoService.sayHello("张三");
            System.out.println(ret);
        }
    }
    ```
    
  - ```java
    package org.example;
    import org.example.service.TestService;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    public class DubboConsumerApp {
        public static void main(String[] args) {
            ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext-dubbo.xml");
            TestService testService = context.getBean("testService", TestService.class);
            testService.test(); // Hello 张三
        }
    }
    ```



