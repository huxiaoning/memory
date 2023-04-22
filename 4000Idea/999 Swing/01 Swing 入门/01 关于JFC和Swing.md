# 关于JFC和Swing

&emsp;&emsp;JFC是Java Foundation Classes的缩写，它包括一组用于构建图形用户界面（GUI）和为Java应用程序添加丰富的图形功能和交互性的特性。它被定义为包含下表中所示的特性。

| 特性             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| Swing GUI组件    | 包括从按钮到分割窗格再到表格的所有内容。许多组件都能够进行排序、打印和拖放等操作，这只是支持功能中的一部分。 |
| 可插拔的外观支持 | Swing应用程序的外观和感觉是可插拔的，可以选择不同的外观和感觉。例如，同一个程序可以使用Java或Windows外观和感觉。此外，Java平台支持GTK+外观和感觉，这使得数百种现有的外观和感觉对Swing程序可用。还有许多其他来源提供的外貌包可供选择。 |
| 可访问性 API     | 使辅助技术（如屏幕阅读器和盲文显示器）能够从用户界面获取信息。 |
| Java 2D API      | 使开发人员能够轻松地在应用程序和小程序中加入高质量的2D图形、文本和图像。Java 2D包括广泛的API，可用于生成并发送高质量输出到打印设备。 |
| 国际化           | 允许开发人员构建可以与全球用户以其自己的语言和文化习惯进行交互的应用程序。借助输入法框架，开发人员可以构建接受使用数千个不同字符（如日语、中文或韩语）的语言中的文本的应用程序。 |

&emsp;&emsp;这个教程集中讲解Swing组件。我们会帮助您选择适合您GUI的组件，告诉您如何使用它们，并为您提供使用它们所需的背景信息。我们还将讨论其他与Swing组件相关的功能。

### 我应该使用哪些Swing包？

&emsp;&emsp;Swing API 强大、灵活，但也非常庞大。Swing API 共有 18 个公共包：

- `javax.accessibility`
- `javax.swing.plaf`
- `javax.swing.text`
- `javax.swing`
- `javax.swing.plaf.basic`
- `javax.swing.text.html`
- `javax.swing.border`
- `javax.swing.plaf.metal`
- `javax.swing.text.html.parser`
- `javax.swing.colorchooser`
- `javax.swing.plaf.multi`
- `javax.swing.text.rtf`
- `javax.swing.event`
- `javax.swing.plaf.synth`
- `javax.swing.tree`
- `javax.swing.filechooser`
- `javax.swing.table`
- `javax.swing.undo`

&emsp;&emsp;幸运的是，大多数程序仅使用API的一小部分。本教程为您整理了API，并提供常见代码示例，指导您需要使用哪些方法和类。本教程中的大部分代码仅使用了一个或两个Swing包：

- `javax.swing`
- `javax.swing.event` (not always required)