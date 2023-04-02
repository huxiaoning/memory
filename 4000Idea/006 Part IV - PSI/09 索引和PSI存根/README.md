# 索引和PSI存根

### 索引

&emsp;&emsp;索引框架提供了一种快速定位特定元素的方法，例如，在大型代码库中包含某个单词的文件或具有特定名称的方法。插件开发者可以使用IDE本身构建的现有索引，并构建和使用自己的索引。

&emsp;&emsp;它支持两种主要类型的索引：

- [基于文件的索引](https://plugins.jetbrains.com/docs/intellij/file-based-indexes.html)
- [存根索引](https://plugins.jetbrains.com/docs/intellij/stub-indexes.html)

&emsp;&emsp;基于文件的索引是直接建立在文件内容之上的。存根索引是建立在序列化的存根树之上的。源文件的存根树是其PSI树的一个子集，其中只包含对外可见的声明，并以紧凑的二进制格式序列化。

&emsp;&emsp;查询一个基于文件的索引可以得到与特定条件相匹配的文件集。查询存根索引可以得到匹配PSI元素的集合。因此，自定义语言插件开发者通常在他们的插件实现中使用存根索引。

> [Index Viewer](https://plugins.jetbrains.com/plugin/13029-index-viewer/)插件可用于检查索引的内容和属性。

### 哑巴模式

&emsp;&emsp;索引是一个潜在的漫长的过程。它是在后台进行的，在这段时间里，IDE的功能被限制在不需要索引的功能上：基本的文本编辑、版本控制等。这种限制是由[`DumbService`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/openapi/project/DumbService.java)管理的。违规行为通过[`IndexNotReadyException`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/openapi/project/IndexNotReadyException.java)报告，关于如何调整调用者，请参见其javadoc。

&emsp;&emsp;`DumbService`提供API来查询IDE当前是否处于“哑”模式（其中不允许索引访问）或“智能”模式（其中所有索引已构建并可以使用）。 它还提供了延迟代码执行直到索引准备就绪的方法。 有关更多详细信息，请参见其JavaDoc。

### 纲要

&emsp;&emsp;有时，以下条件是成立的：

- 基于文件的索引的聚合功能是不需要的。人们只需要根据特定文件的内容计算一些数据，并将其缓存在磁盘上。
- 在索引期间急于计算整个项目的数据是不需要的（例如，它减慢了索引的速度，和/或这个数据可能永远只需要所有项目文件的一个小子集）。
- 数据可以根据要求懒散地重新计算，而不会有明显的性能损失。

&emsp;&emsp;在这种情况下可以使用[基于文件的索引](https://plugins.jetbrains.com/docs/intellij/file-based-indexes.html)，但文件清单提供了一种懒惰地进行数据计算的方法，在磁盘上进行缓存，以及更轻量级的API。请看[VirtualFileGist](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/indexing-api/src/com/intellij/util/gist/VirtualFileGist.java)和[PsiFileGist](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/indexing-api/src/com/intellij/util/gist/PsiFileGist.java)文档。

##### 例子

- `VirtualFileGist`: [ImageInfoIndex](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/images/src/org/intellij/images/index/ImageInfoIndex.java)计算图像尺寸/比特深度，需要显示在用户界面的特定部分。
- `PsiFileGist`: [JavaSimplePropertyGist](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/java/java-indexing-impl/src/com/intellij/psi/impl/JavaSimplePropertyGist.kt)提供Java中的简单属性

### 提升索引性能

##### 性能指标

&emsp;&emsp;在2020.2及以后的版本中，JSON格式的索引性能指标在日志目录中生成（见开发实例的沙盒目录）。从2021.1开始，这些指标还可以以HTML格式提供。

##### 避免使用AST

&emsp;&emsp;如果可能的话，请使用[词法分析器](https://plugins.jetbrains.com/docs/intellij/implementing-lexer.html)信息而不是解析树。

&emsp;&emsp;如果不可能，请使用轻型AST，它不会在内部创建占用内存的AST节点，所以遍历它可能会更快。通过将`FileContent`输入参数转换为[PsiDependentFileContent](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/util/indexing/PsiDependentFileContent.java)并调用`getLighterAST()`来获得[LighterAST](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/lang/LighterAST.java)。请确保只遍历你需要的节点。参见LighterASTNodeVisitor和LightTreeUtil的有用的实用方法。

&emsp;&emsp;对于[存根索引](https://plugins.jetbrains.com/docs/intellij/stub-indexes.html)，实现[`LightStubBuilder`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-impl/src/com/intellij/psi/stubs/LightStubBuilder.java)。

&emsp;&emsp;如果一种自定义语言包含懒散的可解析元素，从不或很少包含任何存根，考虑实现[`StubBuilder.skipChildProcessingWhenBuildingStubs()`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/StubBuilder.java)（最好使用Lexer/node text）。

&emsp;&emsp;在索引XML时，还可以考虑使用[`NanoXmlUtil`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/indexing-impl/src/com/intellij/util/xml/NanoXmlUtil.java)。

