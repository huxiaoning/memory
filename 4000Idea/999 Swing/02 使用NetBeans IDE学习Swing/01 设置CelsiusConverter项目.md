# 设置CelsiusConverter项目

如果您以前使用过NetBeans IDE，那么本节的大部分内容都会很熟悉，因为大多数项目的初始步骤是相似的。但以下步骤描述了特定于此应用程序的设置，请务必仔细遵循它们。

##### 步骤1：创建新项目

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-2-thumb.png)

<center>创建新项目</center>

每个命令的键盘快捷方式出现在每个菜单项的最右侧。NetBeans IDE 的外观和感觉可能因平台而异，但功能将保持不变。

##### 步骤2：选择“常规” -> “Java应用程序”。

接下来，从类别列中选择“常规”，从项目列中选择“Java 应用程序”：

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-3.png)

您可能会在描述窗格中看到“J2SE”的提及；那是现在被称为“Java SE”平台的旧名称。请按标有“下一步”的按钮继续。

##### 第三步：设置项目名称

现在输入“CelsiusConverterProject”作为项目名称。您可以将项目位置和项目文件夹字段保留为默认值，或单击“浏览”按钮选择系统上的其他位置。

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-4.png)

请确保取消选择“创建主类”复选框；如果选择此选项，则会生成一个新的类作为应用程序的主入口点，但是我们在下一步中创建的主GUI窗口将担任该职责，因此不需要勾选此框。完成后，请单击“完成”按钮。

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-5.png)

当IDE加载完成后，您将看到类似于上面的屏幕。除了左上角的项目窗格显示新创建的项目之外，所有窗格都将为空。

##### 步骤4：添加一个JFrame表单

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-6.png)

现在右键单击CelsiusConverterProject名称，选择New -> JFrame Form（JFrame是Swing类，负责应用程序的主框架）。您将在本课程后面学习如何将此类指定为应用程序的入口点。

##### 步骤5：命名GUI类

接下来，输入CelsiusConverterGUI作为类名，learn作为包名。实际上你可以给这个包取任何名称，但是在这里我们遵循教程的惯例，将包命名为它所属的课程名称。

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-7.png)

其余的字段应该自动填写，如上所示。完成后，请单击“完成”按钮。

![](https://docs.oracle.com/javase/tutorial/figures/uiswing/learn/nb-swing-8.png)

当IDE加载完成后，右侧窗格将显示CelsiusConverterGUI的设计时图形视图。在这个屏幕上，您可以直观地拖放和操作各种Swing组件。

