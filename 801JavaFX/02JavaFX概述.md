# JavaFX概述

![image-20231017194047807](https://raw.githubusercontent.com/huxiaoning/img/master/image-20231017194047807.png)



&emsp;&emsp;本章概述了可以使用 JavaFX API 构建的应用程序类型、JavaFX 库的下载位置以及 JavaFX 主要功能的高级信息。

&emsp;&emsp;JavaFX 是一套图形和媒体软件包，使开发人员能够设计、创建、测试、调试和部署丰富的客户端应用程序，这些应用程序可在不同平台上一致运行。

### JavaFX 应用程序

&emsp;&emsp;由于 JavaFX 库是作为 Java API 编写的，因此 JavaFX 应用程序代码可以引用任何 Java 库中的 API。例如，JavaFX 应用程序可以使用 Java API 库访问本地系统功能并连接到基于服务器的中间件应用程序。

&emsp;&emsp;JavaFX 应用程序的外观和风格可以自定义。层叠样式表（CSS）将外观和样式与实现分离开来，这样开发人员就可以专注于编码。图形设计师可以通过 CSS 轻松定制应用程序的外观和样式。如果您有网页设计背景，或者您想将用户界面（UI）和后端逻辑分开，那么您可以用 FXML 脚本语言开发用户界面的表现形式，用 Java 代码开发应用程序的逻辑。如果您更喜欢在不编写代码的情况下设计用户界面，那么请使用 JavaFX 场景生成器。在设计用户界面时，Scene Builder 会创建 FXML 标记，并将其移植到集成开发环境 (IDE)，以便开发人员添加业务逻辑。

### 可用性

&emsp;&emsp;JavaFX API 作为 Java SE 运行时环境 (JRE) 和 Java 开发工具包 (JDK) 的一个完全集成的功能提供。由于 JDK 适用于所有主要桌面平台（Windows、Mac OS X 和 Linux），因此根据 JDK 7 及更高版本编译的 JavaFX 应用程序也可在所有主要桌面平台上运行。JavaFX 8 还提供了对 ARM 平台的支持。JDK for ARM 包括 JavaFX 的基础、图形和控件组件。

&emsp;&emsp;跨平台兼容性为JavaFX应用软件开发人员和用户带来了一致的运行体验。甲骨文确保在所有平台上同步发布和更新，并为运行关键任务应用程序的公司提供广泛的支持计划。

&emsp;&emsp;在 JDK 下载页面，您可以获得 JavaFX 示例应用程序的压缩文件。示例应用程序提供了许多代码示例和片段，通过实例展示了如何编写 JavaFX 应用程序。有关详细信息，请参阅 "如何运行示例应用程序？

### 主要功能

