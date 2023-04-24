# 课程：使用NetBeans IDE学习Swing

&emsp;&emsp;[示例索引](https://docs.oracle.com/javase/tutorial/uiswing/examples/learn/index.html)

&emsp;&emsp;本课程介绍了使用Swing和NetBeans IDE进行图形用户界面(GUI)编程的基础知识。正如您在“[Hello World！](https://docs.oracle.com/javase/tutorial/getStarted/cupojava/netbeans.html)”课程中学到的那样，NetBeans IDE是一个免费、开源、跨平台的集成开发环境，内置支持Java编程语言。与使用文本编辑器编码相比，它提供了许多优势；我们建议尽可能地使用它。如果您还没有阅读上述课程，请立即花点时间阅读一下。它提供了有关下载和安装JDK和NetBeans IDE的有价值信息。

&emsp;&emsp;本课程的目标是通过设计一个简单的应用程序将温度从摄氏度转换为华氏度来介绍Swing API。它的GUI将是基本的，只关注可用Swing组件的子集。我们将使用NetBeans IDE GUI构建器，使用户界面创建变得简单易行，只需拖放即可完成。其自动生成代码功能简化了GUI开发过程，让您专注于应用程序逻辑而不是底层基础设施。

&emsp;&emsp;因为这个课程是一个逐步检查特定操作的清单，我们建议您在阅读时运行NetBeans IDE并执行每个步骤。这将是开始使用Swing编程的最快最简单的方法。如果您无法这样做，仅仅阅读也应该很有用，因为每个步骤都有截图说明。

&emsp;&emsp;如果您更喜欢传统的手动编程方法（不使用IDE辅助），那么可以将本课程视为进入教程其他地方已经提供的低级别讨论的入口。每个讨论中的超链接都会带您前往相关课程，以便了解这些低级别细节。

&emsp;&emsp;这个应用程序完成后的图形用户界面将看起来如下：

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-1.png)

&emsp;&emsp;点击“启动”按钮以使用[Java™ Web Start](https://docs.oracle.com/javase/8/docs/technotes/guides/javaws/)运行CelsiusConverter（[下载JDK 7或更高版本](https://www.oracle.com/java/technologies/downloads/)）。 或者，要自己编译和运行示例，请参阅[示例索引](https://docs.oracle.com/javase/tutorial/uiswing/examples/learn/index.html#CelsiusConverter)。

[&emsp;&emsp;Launch](https://docs.oracle.com/javase/tutorialJWS/samples/uiswing/CelsiusConverterProject/CelsiusConverter.jnlp)

&emsp;&emsp;从最终用户的角度来看，使用非常简单：在文本框中输入温度（摄氏度），点击“转换”按钮，然后观察屏幕上显示的转换后的温度（华氏度）。最小化、最大化和关闭按钮将按预期运行，并且应用程序还将具有出现在窗口顶部的标题。

&emsp;&emsp;从程序员的角度来看，我们将分两个主要阶段编写应用程序。首先，我们将使用各种Swing组件填充GUI并按上面所示排列它们。然后，我们将添加应用逻辑，以便在用户按下“转换”按钮时实际执行转换操作。

&emsp;&emsp;如果您有兴趣使用JavaFX创建GUI，请参阅[JavaFX文档](https://docs.oracle.com/javase/8/javase-clienttechnologies.htm)和[JavaFX-NetBeans Wiki](https://netbeans.apache.org/wiki/JavaFX.html)。

