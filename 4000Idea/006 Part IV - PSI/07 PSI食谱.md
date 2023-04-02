# PSI食谱

&emsp;&emsp;本页给出了使用PSI（程序结构接口）的最常见操作的配方。

&emsp;&emsp;与[开发自定义语言插件](https://plugins.jetbrains.com/docs/intellij/custom-language-support.html)不同，它是关于使用现有语言（如Java）的PSI。

> 请参阅[PSI性能](https://plugins.jetbrains.com/docs/intellij/psi-performance.html)。

### 一般情况

##### 如果我知道一个文件的名称，但不知道其路径，我如何找到它？

[`FilenameIndex.getFilesByName()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/indexing-api/src/com/intellij/psi/search/FilenameIndex.java)

##### 如何查找特定的PSI元素被使用的位置？

[`ReferencesSearch.search()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/indexing-api/src/com/intellij/psi/search/searches/ReferencesSearch.java)

##### 如何重命名PSI元素？

[`RefactoringFactory.createRename()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/lang-api/src/com/intellij/refactoring/RefactoringFactory.java)

##### 如何使虚拟文件的PSI被重建？

[`FileContentUtil.reparseFiles()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/platform/analysis-api/src/com/intellij/util/FileContentUtil.java)

### 特定于Java的功能

> 如果你的插件依赖于Java功能，并以2019.2或更高版本为目标，请务必遵循本博文的步骤。如果你的插件支持其他JVM语言，也可以考虑使用UAST。

##### 如何找到一个类的所有继承者？

[`ClassInheritorsSearch.search()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-indexing-api/src/com/intellij/psi/search/searches/ClassInheritorsSearch.java)

##### 如何通过限定名称找到一个类？

[`JavaPsiFacade.findClass()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/JavaPsiFacade.java)

##### 如何通过简短名称查找类？

[`PsiShortNamesCache.getClassesByName()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-indexing-api/src/com/intellij/psi/search/PsiShortNamesCache.java)

##### 如何找到一个Java类的超类？

[`PsiClass.getSuperClass()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/PsiClass.java)

##### 如何获取Java类所在包的引用？

```java
PsiJavaFile javaFile = (PsiJavaFile) psiClass.getContainingFile();
PsiPackage psiPackage = JavaPsiFacade.getInstance(project)
        .findPackage(javaFile.getPackageName());
```

&emsp;&emsp;或者

[`PsiUtil.getPackageName()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/util/PsiUtil.java)

##### 如何找到重写特定方法的方法？

[`OverridingMethodsSearch.search()`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-indexing-api/src/com/intellij/psi/search/searches/OverridingMethodsSearch.java)

