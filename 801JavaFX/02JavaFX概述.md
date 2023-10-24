# JavaFX概述

![image-20231017194047807](https://raw.githubusercontent.com/huxiaoning/img/master/image-20231017194047807.png)



&emsp;&emsp;[本章](https://docs.oracle.com/javase/8/javafx/get-started-tutorial/jfx-overview.htm#JFXST784)概述了可以使用 JavaFX API 构建的应用程序类型、JavaFX 库的下载位置以及 JavaFX 主要功能的高级信息。

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
- 画布 API。画布 API 可直接在 JavaFX 场景中由一个图形元素（节点）组成的区域内绘图。
- 打印 API。Java SE 8 版本中添加了 javafx.print 包，该包提供了 JavaFX 打印 API 的公共类。
- 丰富的文本支持。JavaFX 8 为 JavaFX 带来了增强的文本支持，包括双向文本和复杂的文本脚本，如控件中的泰语和印度语，以及文本节点中的多行、多风格文本。
- 多点触摸支持。JavaFX 可根据底层平台的功能为多点触控操作提供支持。
- 支持 Hi-DPIJavaFX 8 现在支持 Hi-DPI 显示屏。
- 硬件加速图形管道。JavaFX 图形基于图形渲染管道（Prism）。当与支持的图形卡或图形处理器（GPU）一起使用时，JavaFX 可通过 Prism 快速渲染流畅的图形。如果系统不具备 JavaFX 所支持的推荐 GPU，则 Prism 默认使用软件渲染堆栈。
- 高性能媒体引擎。媒体管道支持网络多媒体内容的播放。它以 GStreamer 多媒体框架为基础，提供了一个稳定、低延迟的媒体框架。
- 自足式应用程序部署模式。自包含应用程序包包含所有应用程序资源以及 Java 和 JavaFX 运行时的私有副本。它们以本地可安装包的形式发布，并提供与该操作系统本地应用程序相同的安装和启动体验。

### 我能用 JavaFX 构建什么？

&emsp;&emsp;使用 JavaFX，您可以构建多种类型的应用程序。通常，它们是网络感知应用程序，可跨平台部署，并以高性能的现代用户界面显示信息，包括音频、视频、图形和动画。

&emsp;&emsp;表 1-1 显示了 JavaFX 8.n 版本中包含的几个 JavaFX 示例应用程序的图片。

表 1-1 JavaFX 应用程序示例

- JavaFX 合奏8

&emsp;&emsp;Ensemble8 是一个示例应用程序库，展示了大量 JavaFX 功能，包括动画、图表和控件。您可以在所有平台上查看每个运行示例并与之互动，还可以阅读其说明。在桌面平台上，您可以复制每个示例的源代码，调整多个示例中使用的示例组件的属性，并在连接到互联网时跟踪相关 API 文档的链接。

- 摩德纳

&emsp;&emsp;Modena 是一个示例应用程序，用于演示使用 Modena 主题的用户界面组件的外观和感觉。您可以选择对比 Modena 和 Caspian 主题，探索这些主题的各个方面。

- 3D 查看器

&emsp;&emsp;3DViewer 是一个示例应用程序，可让您使用鼠标或触控板浏览和检查 3D 场景。3DViewer 具有 OBJ 和 Maya 文件中部分功能的导入程序。Maya 文件也提供了导入动画的功能。(请注意，如果是 Maya 文件，在保存为 Maya 文件时应删除所有对象上的构造历史记录）。

&emsp;&emsp;3DViewer 还可以将场景内容导出为 Java 或 FXML 文件。

### 如何运行示例应用程序？

&emsp;&emsp;本节将介绍如何下载和运行示例应用程序，这些示例应用程序随 Java Platform (JDK 8) 单独下载。

>  请注意：
>
> 在运行 JavaFX 示例应用程序之前，您需要在计算机上安装 JavaFX 运行时库。在执行这些步骤之前，请安装最新版本的 JDK 8 或最新版本的 JRE。

##### 下载并运行示例应用程序：

- 请访问 Java SE 下载页面 http://www.oracle.com/technetwork/java/javase/downloads/。
  - https://www.oracle.com/java/technologies/java-archive-misc-downloads.html#jdk8demos
- 向下滚动，找到 JDK 8 和 JavaFX 演示和示例部分。
- 单击 "演示和样本下载 "按钮进入下载页面。
- 在 Java SE Development Kit 8 Downloads（Java SE 开发工具包 8 下载）页面，向下滚动到 JavaFX Demos and Samples Downloads（JavaFX 演示和示例下载）部分。
- 下载适用于正确操作系统的压缩文件并解压。

&emsp;&emsp;javafx-samples-8.x 目录已创建，其中包含可用示例的文件。样本的 NetBeans 项目位于 javafx-samples-8.x\src 目录中。

- 双击样本的可执行文件。
- 例如，要运行 Ensemble8 预建示例应用程序，双击 Ensemble8.jar 文件。

### 如何在集成开发环境中运行样本？

&emsp;&emsp;您可以使用多种 Java 开发集成开发环境来开发 JavaFX 应用程序。以下步骤介绍了如何在 NetBeans IDE 中查看和运行源代码。

##### 在 NetBeans IDE 中查看并运行示例源代码：

- 如上所述，下载样本并解压文件。
- 在 NetBeans 7.4 或更高版本的集成开发环境中，加载要查看的示例项目。
  - 从 "文件 "菜单中选择 "打开项目"。
  - 在 "打开项目 "对话框中，导航到列出样本的目录。导航路径如下
    - `..\javafx_samples-8.x-<platform>\javafx-samples-8.x\src`
  - 选择要查看的样本。
  - 单击 "打开项目 "按钮。
- 在 "项目 "窗口中，右键单击刚刚打开的项目并选择 "运行"。
  - 注意输出窗口已更新，示例项目已运行并部署。

### 如何创建 JavaFX 应用程序？

&emsp;&emsp;由于 JavaFX 应用程序是用 Java 语言编写的，因此您可以使用自己喜欢的编辑器或任何支持 Java 语言的集成开发环境（IDE）（如 NetBeans、Eclipse 或 IntelliJ IDEA）来创建 JavaFX 应用程序。

##### 创建 JavaFX 应用程序

- 请访问 Java SE 下载页面 http://www.oracle.com/technetwork/java/javase/downloads/ 下载支持 JavaFX 8.n 的 Oracle® JDK 8。该页面上还有认证系统配置和发行说明的链接。
- 使用《JavaFX 示例应用程序入门》创建简单的应用程序，演示如何使用布局、样式表和视觉效果。
- 使用 JavaFX 场景生成器无需编码即可设计 JavaFX 应用程序的用户界面。您可以将用户界面组件拖放到工作区，修改其属性，应用样式表，并将生成的代码与应用程序逻辑集成。
- 从 http://www.oracle.com/technetwork/java/javase/downloads/ 的 JavaFX 下载页面下载 JavaFX 场景生成器。
- 请参阅 JavaFX 场景生成器入门教程了解更多信息。

### 资源

&emsp;&emsp;使用以下资源了解有关 JavaFX 技术的更多信息。

- 从 Java SE 下载页面下载最新的 JDK 8 版本和 JavaFX 示例，网址是
  http://www.oracle.com/technetwork/java/javase/downloads/.
- 阅读[《了解 JavaFX 架构》](https://docs.oracle.com/javase/8/javafx/get-started-tutorial/jfx-architecture.htm#CHDFDAFF)。
- 浏览面向开发人员的 [JavaFX 教程](https://docs.oracle.com/javase/8/javase-clienttechnologies.htm)和文章。

