# 介绍Spring IoC容器和Bean

&emsp;&emsp;本章介绍了Spring框架对控制反转（IoC）原则的实现方式。IoC也被称为依赖注入（DI）。它是一种通过构造函数参数、工厂方法的参数或在对象实例构造或从工厂方法返回后设置的属性来定义它们所依赖的其他对象的过程。容器在创建bean时注入这些依赖项。这个过程从根本上讲，与bean本身使用直接构造类或像服务定位器模式这样的机制来控制其依赖项的实例化或位置的方式相反（因此得名控制反转）。

&emsp;&emsp;org.springframework.beans和org.springframework.context包是Spring框架IoC容器的基础。[BeanFactory](https://docs.spring.io/spring-framework/docs/6.0.6/javadoc-api/org/springframework/beans/factory/BeanFactory.html)接口提供了一个高级的配置机制，可以管理任何类型的对象。[ApplicationContext](https://docs.spring.io/spring-framework/docs/6.0.6/javadoc-api/org/springframework/context/ApplicationContext.html)是BeanFactory的一个子接口。它添加了：

- 更容易集成Spring的AOP特性
- 消息资源处理（用于国际化）
- 事件发布
- 应用程序特定的上下文，如WebApplicationContext，用于Web应用程序。

&emsp;&emsp;简单来说，BeanFactory提供了配置框架和基本功能，而ApplicationContext添加了更多面向企业的功能。 ApplicationContext是BeanFactory的完全超集，并且在描述Spring的IoC容器时本章节中专门使用ApplicationContext。有关使用BeanFactory而不是ApplicationContext的更多信息，请参阅涵盖BeanFactory API的部分。

&emsp;&emsp;在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是由Spring IoC容器实例化、组装和管理的对象。否则，bean只是应用程序中的众多对象之一。bean及其之间的依赖关系反映在容器使用的配置元数据中。