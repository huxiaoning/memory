# 纯配置文件搭建SpringMVC环境

- 1 引入`pom.xml`依赖

  - ```xml
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>5.3.20</version>
            </dependency>
    ```

- 2 配置`src/main/webapp/WEB-INF/web.xml`

  - ```xml
        <servlet>
            <servlet-name>springMVC</servlet-name> <!-- 也指定了springmvc配置文件的默认文件名：/WEB-INF/springMVC-servlet.xml -->
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param><!-- 修改默认的springmvc位置 -->
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:springmvc.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup> <!-- 启动时加载，而不是第一次访问时 -->
        </servlet>
        <servlet-mapping>
            <servlet-name>springMVC</servlet-name>
            <url-pattern>/</url-pattern><!-- 拦截jsp外所有的请求 -->
        </servlet-mapping>
    ```

- 配置`src/main/resources/springmvc.xml`

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    
        <bean id="demoController" class="org.example.controller.DemoController"/>
        <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
            <property name="urlMap">
                <map> <!-- /demo 将映射给demoController执行 -->
                    <entry key="demo" value-ref="demoController"></entry>
                </map>
            </property>
        </bean>
        <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
        <bean id="view" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/"/>
            <property name="suffix" value=".jsp"/>
        </bean>
    </beans>
    ```

- `DemoController.java`

  - ```java
    import org.example.service.UserService;
    import org.springframework.beans.BeansException;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.ApplicationContextAware;
    import org.springframework.web.servlet.ModelAndView;
    import org.springframework.web.servlet.mvc.Controller;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    public class DemoController implements Controller, ApplicationContextAware {
    
        // springmvc容器
        private ApplicationContext applicationContext;
    
        @Override
        public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
            this.applicationContext = applicationContext;
        }
    
        // 这个目前还取不到(后面启用注解支持就好了<mvc:annotation-driven/>)
        @Autowired
        private UserService userService;
    
        @Override
        public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
            System.out.println("执行了springMVC的控制器");
            ModelAndView mav = new ModelAndView("main"); // 默认是请求转发
            // spring容器
            ApplicationContext parent = applicationContext.getParent();
            // 可以取到值
            UserService userService1 = applicationContext.getBean(UserService.class);
            UserService userService2 = parent.getBean(UserService.class);
            System.out.println(userService1 == userService2);
            return mav;
        }
    }
    ```



### Spring容器与SpringMVC容器的关系

Spring容器是SpringMVC容器的父容器。它们是父子关系，子容器(SpringMVC)可以调用父(Spring)容器中的内容。

证据在`org.springframework.web.servlet.FrameworkServlet`中的`createWebApplicationContext()`方法:

```java
	protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
		Class<?> contextClass = getContextClass();
		if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
			throw new ApplicationContextException(
					"Fatal initialization error in servlet with name '" + getServletName() +
					"': custom WebApplicationContext class [" + contextClass.getName() +
					"] is not of type ConfigurableWebApplicationContext");
		}
		ConfigurableWebApplicationContext wac =
				(ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

		wac.setEnvironment(getEnvironment());
		wac.setParent(parent); // 就是这句代码 wac是springmvc容器 parent是spring容器
		String configLocation = getContextConfigLocation();
		if (configLocation != null) {
			wac.setConfigLocation(configLocation);
		}
		configureAndRefreshWebApplicationContext(wac);

		return wac;
	}
```



```java
ApplicationContext parent = class org.springframework.web.context.support.XmlWebApplicationContext;
ConfigurableWebApplicationContext wac = class org.springframework.web.context.support.XmlWebApplicationContext;
```



### 两个接口

- `ConfigurableWebApplicationContext extends WebApplicationContext`
- `WebApplicationContext`
- `ConfigurableWebApplicationContext



### springMVC初始化

`org.springframework.web.servlet.DispatcherServlet`

```java
	protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context);
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
```







### 处理一下中文乱码

`src/main/webapp/WEB-INF/web.xml`

```xml
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

