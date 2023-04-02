# PSI引用

&emsp;&emsp;PSI树中的引用是一个对象，它表示从代码中特定元素的使用到相应声明的链接。解析引用意味着定位特定用法所引用的声明。

最常见的引用类型是由语言语义定义的。例如，考虑一个简单的 Java 方法：

```java
public void hello(String message) {
    System.out.println(message);
}
```

&emsp;&emsp;这个简单的代码片段包含五个引用。由标识符`String`、`System`、`out`和`println`创建的引用可以解析为JDK中的相应声明：`String`和`System`类、`out`字段和`println`方法。由`println(message)`中`message`标识符的第二次出现创建的引用可以解析为在方法头中声明的`message`参数。

&emsp;&emsp;请注意，`String message` 不是引用，因此不能解析。它是一个声明。它不引用在其他地方定义的任何名称；相反，它仅定义一个名称。

&emsp;&emsp;一个引用是一个实现 PsiReference 接口的类的实例。注意，引用与 PSI元素是不同的。由 PSI 元素创建的引用是从 `PsiElement.getReferences()`返回的，引用的底层PSI元素可以从`PsiReference.getElement()`获得。

&emsp;&emsp;调用`PsiReference.resolve()`来解析引用，即找到被引用的声明。理解`PsiReference.getElement()`和`PsiReference.resolve()`之间的区别非常重要。前者方法返回引用的源，而后者返回其目标。在上面的示例中，对于`message`引用，`getElement()`将返回片段第二行上的`message`标识符，而`resolve()`将返回第一行中的`message`标识符（在参数列表内部）。

&emsp;&emsp;解析(resolving)引用的过程与解析(parsing)不同，也不是同时进行的。此外，它并不总是成功的。如果当前在IDE中打开的代码无法编译，或者在其他情况下，PsiReference.resolve() 返回 null 是正常的 - 所有处理引用的代码都必须准备好处理这种情况。

> 请参见“[缓存耗费大量计算的结果](https://plugins.jetbrains.com/docs/intellij/psi-performance.html#cache-results-of-heavy-computations)”。

### 贡献引用

&emsp;&emsp;除了由编程语言语义定义的引用之外，IDE 还识别由代码中使用的 API 和框架的语义确定的许多引用。请考虑以下示例：

```java
File f = new File("foo.txt");
```

&emsp;&emsp;在这个例子中，“foo.txt”从Java语法的角度来看没有特殊的意义，它只是一个字符串字面量。然而，当在IntelliJ IDEA中打开这个例子并在相同的目录下有一个名为“foo.txt”的文件时，可以单击“foo.txt”并导航到该文件。这是因为IDE识别了new File（...）的语义，并将对方法参数中传递的字符串字面量贡献一个引用。

&emsp;&emsp;通常情况下，可以将引用贡献给没有自己引用的元素，例如字符串字面量和注释。引用也经常被贡献给非代码文件，例如 XML 或 JSON。

&emsp;&emsp;贡献引用是扩展现有语言的最常见方法之一。例如，您的插件可以为 Java 代码贡献引用，即使 Java PSI 是平台的一部分，也没有在您的插件中定义。

&emsp;&emsp;实现在 com.intellij.psi.referenceContributor 扩展点中注册的 [PsiReferenceContributor](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiReferenceContributor.java)。

&emsp;&emsp;属性语言应设置为该贡献者适用的语言 ID。然后，使用元素模式在调用 PsiReferenceRegistrar.registerReferenceProvider() 时指定要贡献引用的确切位置。

&emsp;&emsp;请参阅“[Reference Contributor 教程](https://plugins.jetbrains.com/docs/intellij/reference-contributor.html)”。

### 具有可选或多个解析结果的引用

&emsp;&emsp;在最简单的情况下，引用解析为一个元素，如果解析失败，则代码不正确，并且IDE需要将其标记为错误。然而，在某些情况下，情况是不同的。

&emsp;&emsp;第一个案例是软引用。考虑上面的`new File(“foo.txt”)`示例。如果IDE找不到文件“foo.txt”，这并不意味着需要突出显示错误 - 也许该文件仅在运行时可用。此类引用从PsiReference.isSoft()方法返回true，然后可以在检查/注释器中使用它们来完全跳过突出显示或使用较低的严重性。

&emsp;&emsp;第二种情况是多变量引用。考虑一个JavaScript程序的情况。JavaScript是一种动态类型语言，因此IDE不能总是精确地确定在特定位置调用哪个方法。为了处理这个问题，它提供了一个可以解析为多个可能元素的引用。这些引用实现了[PsiPolyVariantReference](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiPolyVariantReference.java)接口。

&emsp;&emsp;要解析 PsiPolyVariantReference，您需要调用其 multiResolve() 方法。该调用将返回一个 [ResolveResult](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/ResolveResult.java) 对象数组。每个对象都标识了一个 PSI 元素，并指定结果是否有效。例如，假设您有多个 Java 方法重载和一次使用不匹配任何重载的参数进行的调用。在这种情况下，您将获得所有重载的 ResolveResult 对象，并且 isValidResult() 对于它们所有都返回 false。

### 搜索引用

&emsp;&emsp;解析引用意味着从使用到相应的声明。要执行相反方向的导航 - 从声明到其用法 - 执行引用搜索。

&emsp;&emsp;使用[ReferencesSearch](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/indexing-api/src/com/intellij/psi/search/searches/ReferencesSearch.java)执行搜索时，需要指定要搜索的元素以及其他可选参数，例如需要在其中搜索引用的范围。创建的[Query](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/util/Query.java)允许一次性获取所有结果或逐个迭代结果。后者允许在找到第一个（匹配）结果后立即停止处理。

### 实现引用

&emsp;&emsp;请参考[指南](https://plugins.jetbrains.com/docs/intellij/references-and-resolve.html)和相应的[教程](https://plugins.jetbrains.com/docs/intellij/reference-contributor.html)以获取更多信息。