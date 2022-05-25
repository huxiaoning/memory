# Ioc控制反转

Inversion of Control 控制反转：

对创建对象这件事情的控制权，反转了。

以前是自已new对象，现在交给spring Ioc容器来控制。



### 简单示例

- `People`类

  - ```java
    public class People {
        private int id;
        private String name;
    	// ignore get set and toString ...
    }
    
    ```

- `src/main/resources/applicationContext.xml`

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean class="org.example.pojo.People"/>
    </beans>
    ```

- `main()`

  - ```java
            ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
            People people = context.getBean(People.class);
            System.out.println(people);
    ```

  - ```java
            // 获取spring管理的所有对象的名称(id)
            String[] beanDefinitionNames = context.getBeanDefinitionNames();
            for (String beanDefinitionName : beanDefinitionNames) {
                System.out.println(beanDefinitionName);
            }
            int beanDefinitionCount = context.getBeanDefinitionCount();
            System.out.println(beanDefinitionCount);
          
            System.out.println(context.containsLocalBean("people"));
    ```

  

### `ApplicationContext`相关接口

- `ApplicationContext`

```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
	@Nullable
	String getId();
	String getApplicationName();
	String getDisplayName();
	long getStartupDate();
	@Nullable
	ApplicationContext getParent();
	AutowireCapableBeanFactory getAutowireCapableBeanFactory() throws IllegalStateException;
}

```

- `BeanFactory`

```java
public interface BeanFactory {
	String FACTORY_BEAN_PREFIX = "&";
	Object getBean(String name) throws BeansException;
	<T> T getBean(String name, Class<T> requiredType) throws BeansException;
	Object getBean(String name, Object... args) throws BeansException;
	<T> T getBean(Class<T> requiredType) throws BeansException;
	<T> T getBean(Class<T> requiredType, Object... args) throws BeansException;
	<T> ObjectProvider<T> getBeanProvider(ResolvableType requiredType);
	boolean containsBean(String name);
	boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
	boolean isPrototype(String name) throws NoSuchBeanDefinitionException;
	boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException
	boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;
	@Nullable
	Class<?> getType(String name) throws NoSuchBeanDefinitionException;
	@Nullable
	Class<?> getType(String name, boolean allowFactoryBeanInit) throws NoSuchBeanDefinitionException;
	String[] getAliases(String name);
}
```

