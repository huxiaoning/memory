# Spring指的是什么

&emsp;&emsp;“Spring”一词在不同的语境中有不同的含义。它可以用来指代Spring Framework项目本身，这就是一切的起点。随着时间的推移，其他基于Spring Framework构建的Spring项目也相继涌现。当人们提到“Spring”时，他们通常指的是整个Spring项目家族。本参考文档关注的是基础：Spring Framework本身。

&emsp;&emsp;Spring Framework被划分为不同的模块，应用程序可以选择所需的模块。核心容器模块包括配置模型和依赖注入机制。除此之外，Spring Framework还为不同的应用程序架构提供了基础支持，包括消息传递、事务性数据和持久性以及Web。它还包括基于Servlet的Spring MVC Web框架和同时开发的Spring WebFlux反应式Web框架。

&emsp;&emsp;关于模块的说明：Spring框架的JAR文件支持部署到JDK 9的模块路径（“Jigsaw”）。为了在Jigsaw启用的应用程序中使用，Spring Framework 5 JAR文件附带了“Automatic-Module-Name”清单条目，这些条目定义了稳定的语言级别模块名称（“spring.core”、“spring.context”等），与JAR工件名称无关（这些JAR遵循相同的命名模式，用“-”代替“.”，例如“spring-core”和“spring-context”）。当然，Spring框架的JAR文件在JDK 8和9+的类路径上也可以正常工作。