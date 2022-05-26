# Spring整合MyBatis

- 1 `pom.xml`

  - ```xml
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>5.3.20</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>5.3.20</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>5.3.20</version>
            </dependency>
            <dependency><!-- DriverManagerDataSource -->
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>5.3.20</version>
            </dependency>
            <dependency> <!-- SqlSessionFactoryBean -->
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis-spring</artifactId>
                <version>2.0.7</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.5.9</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.49</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.17</version>
            </dependency>
    ```

- 2 `src/main/resources/applicationContext.xml`

  - ```xml
        <bean id="driverManagerDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
            <property name="url" value="jdbc:mysql://127.0.0.1:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
            <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        </bean>
        <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
            <property name="dataSource" ref="driverManagerDataSource"/>
            <property name="typeAliasesPackage" value="org.example.entity"/>
        </bean>
        <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
            <property name="basePackage" value="org.example.mapper"/>
            <!--
            sqlSessionFactory过期了,用下面的sqlSessionFactoryBeanName
            <property name="sqlSessionFactory" ref="sqlSessionFactoryBean"/>
            -->
            <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
        </bean>
    ```

- 3 `MyBatis正常开发代码`

  - ```java
    package org.example.entity;
    import java.io.Serializable;
    import java.util.Date;
    public class User implements Serializable {
        private static final long serialVersionUID = 4934703200775206026L;
        private Integer id;
        private String userName;
        private Integer age;
        private Integer sex;
        private Date birth;
        private String mobile;
        private Integer createUserId;
        private Date createTime;
        private Integer updateUserId;
        private Date updateTime;
       	// get set toString
    }
    ```

  - ```java
    package org.example.mapper;
    import org.example.entity.User;
    import java.util.List;
    public interface UserMapper {
        List<User> findAll();
    }
    ```

  - ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="org.example.mapper.UserMapper">
        <select id="findAll" resultType="user">
            SELECT id,
                   user_name      AS userName,
                   age,
                   sex,
                   birth,
                   mobile,
                   create_user_id AS createUserId,
                   create_time    AS createTime,
                   update_user_id AS updateUserId,
                   update_time    AS updateTime
            FROM t_user
        </select>
    </mapper>
    ```

- 测试代码

  - ```java
    package org.example;
    import org.example.entity.User;
    import org.example.mapper.UserMapper;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    import java.util.List;
    public class App {
        public static void main(String[] args) {
            ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
            /**
             * dataSource
             * sqlSessionFactoryBean
             * org.mybatis.spring.mapper.MapperScannerConfigurer#0
             * userMapper (居然已经管理了userMapper)
             * org.springframework.context.annotation.internalConfigurationAnnotationProcessor
             * org.springframework.context.annotation.internalAutowiredAnnotationProcessor
             * org.springframework.context.annotation.internalCommonAnnotationProcessor
             * org.springframework.context.event.internalEventListenerProcessor
             * org.springframework.context.event.internalEventListenerFactory
             */
            String[] beanDefinitionNames = context.getBeanDefinitionNames();
            for (String beanDefinitionName : beanDefinitionNames) {
                System.out.println(beanDefinitionName);
            }
            
            UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
            List<User> userList = userMapper.findAll();
            for (User user : userList) {
                System.out.println(user);
            }
        }
    }
    ```



### 变化

- `mybatis-config.xml`不见了
- 直接从Spring容器中获取Mapper接口的代理实例对象。
- 缺少声明式事务



问题：

`mybatis-spring`是怎么把代理对象放入spring容器的呢？spring有没有类似的监听器可以监听一下这个事？



### 再整合一下`Servlet`

- `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.example</groupId>
    <artifactId>spring-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging> <!-- 打包方式更改为war -->
    <name>spring-demo</name>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency> <!-- servlet -->
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency> <!-- jstl -->
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        
        <dependency> <!-- ContextLoaderListener -->
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.3.20</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.3.20</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.3.20</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.20</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.20</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.7</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.9</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>
</project>
```

- `src/main/webapp/WEB-INF/web.xml`

  - ```xml
    <web-app>
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </context-param>
        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
    </web-app>
    ```

- `TestServlet`

  - ```java
    @WebServlet(name = "TestServlet", value = "/TestServlet")
    public class TestServlet extends HttpServlet {
        private UserMapper userMapper;
        @Override
        public void init() throws ServletException {
            WebApplicationContext context = WebApplicationContextUtils.getRequiredWebApplicationContext(getServletContext());
            userMapper = context.getBean("userMapper", UserMapper.class);
        }
        @Override
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            doPost(request, response);
        }
        @Override
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            List<User> userList = userMapper.findAll();
            for (User user : userList) {
                System.out.println(user);
            }
        }
    }
    ```



