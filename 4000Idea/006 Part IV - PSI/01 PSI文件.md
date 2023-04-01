# PSI文件

&emsp;&emsp;一个 PSI文件是表示文件内容的层次结构的根，以特定编程语言中的元素层次结构形式呈现。

&emsp;&emsp;[PsiFile类](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiFile.java)是所有PSI文件的通用基类，而特定语言的文件通常由其子类表示。例如，[PsiJavaFile类](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/PsiJavaFile.java)表示Java文件，而[XmlFile类](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/xml-psi-api/src/com/intellij/psi/xml/XmlFile.java)表示XML文件。

&emsp;&emsp;与具有应用程序范围（即使打开多个项目，每个文件也由相同的VirtualFile实例表示）的[虚拟文件](https://plugins.jetbrains.com/docs/intellij/virtual-file.html)和[文档](https://plugins.jetbrains.com/docs/intellij/documents.html)不同，PSI具有项目范围：如果文件属于同时打开的多个项目，则同一文件由多个PsiFile实例表示。

### 如何获取一个PsiFile实例？

| 上下文                                                       | API                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Action](https://plugins.jetbrains.com/docs/intellij/basic-action-system.html) | [`AnActionEvent.getData(CommonDataKeys.PSI_FILE)`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnActionEvent.java) |
| [Document](https://plugins.jetbrains.com/docs/intellij/documents.html) | [`PsiDocumentManager.getPsiFile()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiDocumentManager.java) |
| [PSI Element](https://plugins.jetbrains.com/docs/intellij/psi-elements.html) | [`PsiElement.getContainingFile()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiElement.java) (如果PSI元素不包含在文件中，则可能返回null) |
| [Virtual File](https://plugins.jetbrains.com/docs/intellij/virtual-file.html) | [`PsiManager.findFile()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiManager.java), [`PsiUtilCore.toPsiFiles()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/util/PsiUtilCore.java) |
| File Name                                                    | [`FilenameIndex.getVirtualFilesByName()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/indexing-api/src/com/intellij/psi/search/FilenameIndex.java) and locate via [`PsiManager.findFile()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiManager.java) or [`PsiUtilCore.toPsiFiles()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/core-api/src/com/intellij/psi/util/PsiUtilCore.java) |

### 可以用PsiFile对象做什么？

&emsp;&emsp;大多数有趣的修改操作是在单个PSI元素的级别上执行的，而不是整个文件的级别。

&emsp;&emsp;要迭代文件中的元素，请使用

```java
psiFile.accept(new PsiRecursiveElementWalkingVisitor() {
  // visitor implementation ...
});
```

请参阅[导航PSI](https://plugins.jetbrains.com/docs/intellij/navigating-psi.html)。

### PSI文件来自哪里？

&emsp;&emsp;由于PSI依赖于语言，因此需要使用[Language](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/lang/Language.java)实例来创建Psl文件：

```java
LanguageParserDefinitions.INSTANCE
    .forLanguage(MyLanguage.INSTANCE)
    .createFile(fileViewProvider);
```

&emsp;&emsp;与文档类似，当访问特定文件的 PSI 时，PSI 文件会按需创建。

### PSI文件会持续多久？

&emsp;&emsp;像文档一样，PSI文件从相应的VirtualFile实例中弱引用，并且如果没有任何人引用它们，则可以进行垃圾回收。

### 如何创建PSl文件？

&emsp;&emsp;`PsiFileFactory.createFileFromText()` 方法创建一个带有指定内容的内存中的 PSl 文件。
要将 Psl 文件保存到磁盘，请使用 `PsiDirectory.add()` 方法。

### 当PSl文件发生更改时，我该如何收到通知？

&emsp;&emsp;`PsiManager.addPsiTreeChangeListener()` 允许您接收有关项目PSI树的所有更改的通知。或者，可以在 `com.intellij.psi.treeChangeListener` 扩展点中注册 `PsiTreeChangeListener`。

> 请查看 PsiTreeChangeEvent Javadoc，了解处理 PSI 事件时常见的问题。

### 我如何扩展PSI?

&emsp;&emsp;PSI可以通过自定义语言插件来支持其他语言。有关开发自定义语言插件的更多详细信息，请参阅《自定义语言支持参考指南》。

### 与PSI合作的规则是什么？

&emsp;&emsp;PSI文件内容的任何更改都会反映在文档中，因此所有与文档相关的工作规则（读/写操作、命令、只读状态处理）均有效。
