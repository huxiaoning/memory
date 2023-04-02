# PSI性能

&emsp;&emsp;另见[避免UI冻结](https://plugins.jetbrains.com/docs/intellij/general-threading-rules.html#avoiding-ui-freezes)和[提高索引性能](https://plugins.jetbrains.com/docs/intellij/indexing-and-psi-stubs.html#improving-indexing-performance)。

> [IDE Perf](https://plugins.jetbrains.com/plugin/15104-ide-perf)插件提供即时的性能诊断工具，包括[CachedValue](https://plugins.jetbrains.com/docs/intellij/psi-performance.html#cache-results-of-heavy-computations)指标的专用视图。

### 避免使用昂贵的PsiElement方法

&emsp;&emsp;避免在深度树结构中使用昂贵的PsiElement方法。

&emsp;&emsp;`getText()`遍历给定元素下的整个树并连接字符串，考虑使用`textMatches()`代替。

&emsp;&emsp;`getTextRange()`, `getContainingFile()`, 和`getProject()`可以遍历树上的文件，这在非常嵌套的树中可能很长。如果你只需要PSI元素的长度，使用`getTextLength()`。

&emsp;&emsp;`getContainingFile()`和`getProject()`通常可以在任务的执行过程中计算一次，然后存储在字段中或通过参数传递。

&emsp;&emsp;此外，诸如`getText()`、`getNode()`或`getTextRange()`等方法都需要AST，获取AST可能是一个相当昂贵的操作，见下节。

### 避免使用过多的PSI树/文档

&emsp;&emsp;避免同时向内存加载太多的解析树或文件。理想情况下，只有在编辑器中打开的文件的AST节点应该出现在内存中。其他的东西，即使是为了解析/加亮而需要的，也可以通过PSI接口访问，但它的实现应该使用下面的[存根](https://plugins.jetbrains.com/docs/intellij/stub-indexes.html)，这对CPU和内存的消耗较少。

&emsp;&emsp;如果存根不太适合你的情况（例如，你需要的信息很大和/或很少需要，或者你正在为一种你无法控制的PSI语言开发一个插件），你可以创建一个[自定义索引或Gist](https://plugins.jetbrains.com/docs/intellij/indexing-and-psi-stubs.html)。

&emsp;&emsp;为确保您不会意外加载AST，您可以在生产中使用AstLoadingFilter，在测试中使用PsiManagerEx.setAssertOnFileLoadingFilter()。

&emsp;&emsp;这同样适用于文档：只有在编辑器中打开的文档才应该被加载。通常情况下，你不应该需要文档内容（因为大多数信息可以从PSI中检索到）。如果你还是需要文档，可以考虑将你需要提供的信息保存在一个[自定义的索引或gist](https://plugins.jetbrains.com/docs/intellij/indexing-and-psi-stubs.html)中，以便以后更方便地获取。如果你仍然需要文档，那么至少要确保你一个一个地加载它们，不要把它们放在强引用上，让GC尽快释放内存。

### 缓存需要大量计算的结果

&emsp;&emsp;诸如`PsiElement.getReference()`（和`getReferences()`）、`PsiReference.resolve()`（和`multiResolve()`及其他等价物）的方法调用或表达式类型、类型推理结果、控制流图等的计算可能很昂贵。为了避免多次支付这种费用，这种计算的结果可以被缓存和重复使用。通常情况下，用[CachedValueManager](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/util/CachedValuesManager.java)创建的[CachedValue](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/util/CachedValue.java)可以很好地实现这一目的。

&emsp;&emsp;如果你缓存的信息只取决于当前PSI元素的子树（而不是其他：没有解析结果或其他文件），你可以在你的PsiElement实现中的一个字段中进行缓存，并在`ASTDelegatePsiElement.subtreeChanged()`的覆盖中放弃缓存。