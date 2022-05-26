# SpingMVC注解方式搭建

- pom依赖与web.xml与前一节没有不同。

- `src/main/resources/springmvc.xml`

  - ```xml
        <context:component-scan base-package="org.example.controller"/>
        <!--
            HandlerMapping
            HandlerAdapter
        -->
        <mvc:annotation-driven/>
        <!--
            springMVC会处理除.jsp外的所有请求，包括静态资源js css html
            这些静态资源应该放行
        -->
        <mvc:resources mapping="/js/**" location="/js/"></mvc:resources>
        <mvc:resources mapping="/css/**" location="/css/"></mvc:resources>
        <mvc:resources mapping="/img/**" location="/img/"></mvc:resources>
    ```

- `src/main/resources/applicationContext.xml`

  - ```xml
        <context:component-scan base-package="org.example">
            <context:exclude-filter type="annotation"
                                    expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>
    ```

  - spring容器不管理mvc容器中的控制器

- `DemoController`

  - ```java
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    @Controller
    public class DemoController {
        @RequestMapping("/demo")
        public String demo() {
            System.out.println("执行了springMVC的控制器");
            return "main.jsp";
        }
    }
    ```

