# PSI元素

&emsp;&emsp;PSI（程序结构接口）文件表示一组PSI元素（即所谓的PSI树）的层次结构。单个[PSI文件](https://plugins.jetbrains.com/docs/intellij/psi-files.html)（本身也是一个PSI元素）可以在特定的编程语言中公开多个PSI树（请参见[文件视图提供程序](https://plugins.jetbrains.com/docs/intellij/file-view-providers.html)）。一个PSI元素可以有子PSI元素。

&emsp;&emsp;PSI元素和在单个PSI元素级别上的操作用于探索由IntelliJ平台解释的源代码的内部结构。例如，您可以使用PSI元素执行代码分析，如[代码检查](https://www.jetbrains.com/help/idea/code-inspection.html?_ga=2.218447899.328034752.1680264586-1773504485.1679713598&_gl=1*18qgy31*_ga*MTc3MzUwNDQ4NS4xNjc5NzEzNTk4*_ga_9J976DJZ68*MTY4MDI3NTcxNC43LjEuMTY4MDI3NTczOS4zNS4wLjA.)或[意向操作](https://www.jetbrains.com/help/idea/intention-actions.html?_ga=2.17277371.328034752.1680264586-1773504485.1679713598&_gl=1*9vr0dx*_ga*MTc3MzUwNDQ4NS4xNjc5NzEzNTk4*_ga_9J976DJZ68*MTY4MDI3NTcxNC43LjEuMTY4MDI3NTg1Ni40OC4wLjA.)。

&emsp;&emsp;[PsiElement](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiElement.java) 类是 PSI 元素的通用基类。

### 我如何获取PSI元素?

| 上下文                                                       | API                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Action](https://plugins.jetbrains.com/docs/intellij/basic-action-system.html) | [`AnActionEvent.getData(CommonDataKeys.PSI_ELEMENT)`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnActionEvent.java)<br />注意：如果编辑器当前已打开且插入符下的元素是引用，则将返回解析引用的结果。 |
| [PSI File](https://plugins.jetbrains.com/docs/intellij/psi-files.html) | `PsiFile.findElementAt(offset)` 方法可以返回指定偏移量处的叶子元素，通常是词法分析器标记。要找到精确类型的元素，可以使用 `PsiTreeUtil.getParentOfType()` 方法。<br />[`PsiRecursiveElementWalkingVisitor`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiRecursiveElementWalkingVisitor.java) |
| [Reference](https://plugins.jetbrains.com/docs/intellij/psi-references.html) | [`PsiReference.resolve()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiReference.java) |

### 我可以用PSI元素做什么？

&emsp;&emsp;请查看[PSI食谱](https://plugins.jetbrains.com/docs/intellij/psi-cookbook.html)。