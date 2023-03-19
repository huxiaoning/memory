# Spring和Spring Framework的历史

Spring诞生于2003年，作为对早期J2EE规范复杂性的回应。尽管有些人认为Java EE及其现代化的继任者Jakarta EE与Spring相互竞争，但它们实际上是相辅相成的。Spring编程模型不采用Jakarta EE平台规范；相反，它与传统EE范围内精心选择的单个规范集成：

- Servlet API（JSR 340）

- WebSocket API（JSR 356）

- Concurrency Utilities（JSR 236）

- JSON Binding API（JSR 367）

- Bean Validation（JSR 303）

- JPA（JSR 338）

- JMS（JSR 914）

- 以及必要时进行事务协调的JTA/JCA设置。

Spring框架还支持依赖注入（JSR 330）和通用注解（JSR 250）规范，应用程序开发人员可以选择使用它们，而不是Spring框架提供的特定机制。最初，它们基于常见的javax包。

从Spring Framework 6.0开始，Spring已升级到Jakarta EE 9级别（例如Servlet 5.0+，JPA 3.0+），基于jakarta命名空间而不是传统的javax包。以EE 9作为最低要求，已经支持了EE 10，Spring已经准备好为Jakarta EE API的进一步发展提供开箱即用的支持。Spring Framework 6.0与Tomcat 10.1、Jetty 11和Undertow 2.3等Web服务器完全兼容，同时也与Hibernate ORM 6.1兼容。

随着时间的推移，Java/Jakarta EE在应用程序开发中的角色发生了变化。在J2EE和Spring早期，应用程序被创建为部署到应用服务器。如今，借助Spring Boot的帮助，应用程序以devops和云友好的方式创建，Servlet容器被嵌入并且容易更改。自Spring Framework 5以来，WebFlux应用程序甚至不直接使用Servlet API，并且可以运行在不是Servlet容器的服务器（例如Netty）上。

Spring 继续创新和发展。除了 Spring Framework，还有其他项目，如 Spring Boot、Spring Security、Spring Data、Spring Cloud、Spring Batch 等等。需要记住的是，每个项目都有自己的源代码存储库、问题跟踪器和发布节奏。请访问 spring.io/projects 获取完整的 Spring 项目列表。