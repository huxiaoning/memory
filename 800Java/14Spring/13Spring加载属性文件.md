# Spring加载属性文件



- `db.properties`

  - ```properties
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://127.0.0.1:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSL=false
    jdbc.username=root
    jdbc.password=123456
    ```

- `applicationContext.xml`

  - ```xml
        <context:property-placeholder location="classpath:db.properties"/> <!-- 加载多个用逗号隔开 -->
        <bean id="driverManagerDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
            <property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
            <property name="driverClassName" value="${jdbc.driver}"/>
        </bean>
    ```

- 也可以在bean类型中取值

  - ```java
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;
    @Component
    public class XxxComponent {
        @Value("${jdbc.driver}") //  @Value注解也需要当前类被扫描 <context:component-scan base-package="org.example"/>
        private String driver;
    }
    ```



 
