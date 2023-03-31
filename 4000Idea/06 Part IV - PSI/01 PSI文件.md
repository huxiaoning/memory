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

