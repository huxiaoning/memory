# 容器概述

&emsp;&emsp;org.springframework.context.ApplicationContext接口代表了Spring IoC容器，负责实例化、配置和组装bean。容器通过读取配置元数据来获取有关实例化、配置和组装哪些对象的指令。配置元数据可以使用XML、Java注解或Java代码表示。它允许您表达组成应用程序的对象及其之间的丰富相互依赖关系。

&emsp;&emsp;Spring提供了ApplicationContext接口的多个实现。在独立应用程序中，通常创建ClassPathXmlApplicationContext或FileSystemXmlApplicationContext的实例。虽然XML一直是定义配置元数据的传统格式，但您可以通过提供少量XML配置来声明性地启用对这些附加元数据格式的支持，从而指示容器使用Java注解或代码作为元数据格式。

&emsp;&emsp;在大多数应用程序场景中，通常不需要显式的用户代码来实例化一个或多个Spring IoC容器。例如，在Web应用程序场景中，应用程序的web.xml文件中的简单的八行（左右）样板Web描述符XML通常就足够了（请参见Web应用程序的方便的ApplicationContext实例化）。如果您使用Spring Tools for Eclipse（一种基于Eclipse的开发环境），您可以通过几个鼠标点击或按键轻松创建这个样板配置。

&emsp;&emsp;以下图表显示了Spring的高级视图。您的应用程序类与配置元数据结合在一起，这样，在创建和初始化ApplicationContext之后，您就拥有一个完全配置和可执行的系统或应用程序。

![container magic](https://docs.spring.io/spring-framework/docs/6.0.6/reference/html/images/container-magic.png)

