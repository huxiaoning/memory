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

&emsp;&emsp;JavaFX 8 及其后续版本包含以下功能。JavaFX 8 版本中引入的项目会相应标出：

- Java API。JavaFX 是一个 Java 库，由用 Java 代码编写的类和接口组成。这些应用程序接口旨在成为 Java 虚拟机（Java VM）语言（如 JRuby 和 Scala）的友好替代品。

- FXML 和场景生成器。FXML 是一种基于 XML 的声明式标记语言，用于构建 JavaFX 应用程序用户界面。设计人员可以用 FXML 编写代码，也可以使用 JavaFX 场景生成器交互式地设计图形用户界面（GUI）。Scene Builder 生成的 FXML 标记可移植到集成开发环境，开发人员可在其中添加业务逻辑。

- WebView。它是一种网络组件，使用 WebKitHTML 技术可在 JavaFX 应用程序中嵌入网页。在 WebView 中运行的 JavaScript 可调用 Java API，而 Java API 可调用在 WebView 中运行的 JavaScript。JavaFX 8 中添加了对其他 HTML5 功能的支持，包括 Web Sockets、Web Workers 和 Web 字体以及打印功能。 请参见在 JavaFX 应用程序中添加 HTML 内容。
- Swing 互操作性。现有的 Swing 应用程序可使用 JavaFX 功能进行更新，如丰富的图形媒体播放和嵌入式 Web 内容。SwingNode 类可让您将 Swing 内容嵌入 JavaFX 应用程序，该类已添加到 JavaFX 8 中。 有关详细信息，请参阅 SwingNode API javadoc 和在 JavaFX 应用程序中嵌入 Swing 内容。
- 内置用户界面控件和 CSS。JavaFX 提供了开发全功能应用程序所需的所有主要用户界面控件。组件可以使用 CSS 等标准 Web 技术进行换肤。DatePicker 和 TreeTableView UI 控件现已随 JavaFX 8 版本发布。有关详细信息，请参阅使用 JavaFX UI 控件。此外，CSS Styleable* 类已成为公共 API，允许使用 CSS 对对象进行样式设置。
- 摩德纳主题Modena 主题取代了 Caspian 主题，成为 JavaFX 8 应用程序的默认主题。通过在应用程序 start() 方法中添加 setUserAgentStylesheet(STYLESHEET_CASPIAN) 行，您仍可使用 Caspian 主题。如需了解更多信息，请访问 fxexperience.com 上的 Modena 博客。
- 3D 图形功能。JavaFX 8 的三维图形库中新增了 Shape3D（Box、Cylinder、MeshView 和 Sphere 子类）、SubScene、Material、PickResult、LightBase（AmbientLight 和 PointLight 子类）和 SceneAntialiasing 的 API 类。 在此版本中还更新了 Camera API 类。更多信息，请参阅 JavaFX 三维图形入门文档以及 javafx.scene.shape.Shape3D、javafx.scene.SubScene、javafx.scene.paint.Material、javafx.scene.input.PickResult 和 javafx.scene.SceneAntialiasing 的相应 API javadoc。
