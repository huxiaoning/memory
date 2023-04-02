# 修改PSI

&emsp;&emsp;PSI 是源代码的读写表示形式，它是与源文件结构相对应的元素树。您可以通过添加、替换和删除 PSI 元素来修改 PSI。

&emsp;&emsp;要执行这些操作，您可以使用诸如PsiElement.add()、PsiElement.delete()和PsiElement.replace()等方法，以及在[PsiElement](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiElement.java)接口中定义的其他方法，这些方法允许您在单个操作中处理多个元素或指定需要添加元素的树中确切位置。

&emsp;&emsp;与文档操作类似，PSI修改需要包装在写入操作和命令中（并且只能在事件分派线程中执行）。有关命令和写入操作的更多信息，请参阅“[文档](https://plugins.jetbrains.com/docs/intellij/documents.html#what-are-the-rules-of-working-with-documents)”文章。

### 创建新的PSI

&emsp;&emsp;要添加到树中或替换现有PSI元素的PSI元素通常是从文本创建的。在最普通的情况下，您使用[PsiFileFactory](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiFileFactory.java)的createFileFromText()方法创建一个包含所需的代码构造的新文件，以将其添加到树中或用作现有元素的替换，遍历生成的树以定位所需的特定部分，然后将该元素传递给add()或replace()。参见[如何创建PSI文件](https://plugins.jetbrains.com/docs/intellij/psi-files.html#how-do-i-create-a-psi-file)？

&emsp;&emsp;大多数编程语言都提供了工厂方法，让你更容易地创建特定的代码结构。例如：

- [PsiJavaParserFacade](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/PsiJavaParserFacade.java)类包含诸如`createMethodFromText()`之类的方法，该方法可以从给定的文本创建一个Java方法。
- [SimpleElementFactory.createProperty()](https://github.com/JetBrains/intellij-sdk-code-samples/blob/main/simple_language_plugin/src/main/java/org/intellij/sdk/language/psi/SimpleElementFactory.java)创建一个简单语言属性

&emsp;&emsp;当你实现重构、意图或检查快速修复，这些工作需要与现有代码一起工作，你通过各种createFromText()方法传递的文本将结合硬编码的碎片和从现有文件中取出的代码碎片。对于小的代码碎片（单个标识符），你可以简单地将现有代码的文本附加到你正在构建的代码碎片的文本中。在这种情况下，你需要确保生成的文本在语法上是正确的。否则，createFromText()方法将抛出一个异常。

&emsp;&emsp;对于较大的代码片段，最好分几步进行修改：

- 从文本中创建一个替换的树形片段，为用户的代码片段留下占位符；
- 用用户代码片段替换占位符；
- 用替换树替换原始源文件中的元素。

&emsp;&emsp;这确保了用户代码的格式保持不变，并且修改不会引入任何不必要的空格变化。就像IntelliJ平台API的其他地方一样，传递给`createFileFromText()`和其他`createFromText()`方法的文本必须仅使用`\n`作为行分隔符。

&emsp;&emsp;作为这种方法的一个例子，请看`ComparingStringReferencesInspection`例子中的[快速修复](https://plugins.jetbrains.com/docs/intellij/code-inspections.html)：

```java
// binaryExpression holds a PSI expression of the form "x == y", which needs to be replaced with "x.equals(y)"
PsiBinaryExpression binaryExpression = (PsiBinaryExpression) descriptor.getPsiElement();
IElementType opSign = binaryExpression.getOperationTokenType();
PsiExpression lExpr = binaryExpression.getLOperand();
PsiExpression rExpr = binaryExpression.getROperand();

// Step 1: Create a replacement fragment from text, with "a" and "b" as placeholders
PsiElementFactory factory = JavaPsiFacade.getInstance(project).getElementFactory();
PsiMethodCallExpression equalsCall =
    (PsiMethodCallExpression) factory.createExpressionFromText("a.equals(b)", null);

// Step 2: replace "a" and "b" with elements from the original file
equalsCall.getMethodExpression().getQualifierExpression().replace(lExpr);
equalsCall.getArgumentList().getExpressions()[0].replace(rExpr);

// Step 3: replace a larger element in the original file with the replacement tree
PsiExpression result = (PsiExpression) binaryExpression.replace(equalsCall);
```

### 维护树结构的一致性

&emsp;&emsp;PSI的修改方法并不限制你构建结果树结构的方式。例如，在处理一个Java类时，你可以添加一个for语句作为PsiMethod元素的直接子元素，尽管Java解析器永远不会产生这样的结构（for语句永远是PsiCodeBlock的子元素）代表方法体。产生不正确的树状结构的修改可能看起来是有效的，但它们将导致以后的问题和异常。因此，你总是需要确保你用PSI修改操作建立的结构与解析器在解析你所创建的代码时产生的结构相同。

&emsp;&emsp;为了确保你没有引入不一致，你可以在修改PSI的动作的测试中调用`PsiTestUtil.checkFileStructure()`。这个方法可以确保你建立的结构与解析器产生的结构是一样的。

### 空白(格)和导入

&emsp;&emsp;当使用PSI修改函数时，你永远不应该从文本中创建单独的空白节点（空格或换行）。相反，所有的空白修改都由格式化器执行，它遵循用户选择的代码风格设置。格式化是在每个命令的末尾自动执行的，如果需要，也可以使用CodeStyleManager类中的reformat(PsiElement)方法手动执行。

&emsp;&emsp;另外，在处理Java代码时（或处理其他具有类似导入机制的语言的代码，如Groovy或Python），你不应该手动创建导入。相反，你应该在你生成的代码中插入完全限定的名称，然后调用`JavaCodeStyleManager`中的[shortenClassReferences()](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/codeStyle/JavaCodeStyleManager.java)方法（或者你正在使用的语言的同等API）。这可以确保导入是根据用户的代码风格设置创建的，并插入到文件的正确位置。

### 将PSI与文档修改结合起来

&emsp;&emsp;在某些情况下，你需要进行PSI修改，然后通过PSI对刚刚修改的文档进行操作（例如，启动一个[实时模板](https://plugins.jetbrains.com/docs/intellij/live-templates.html)）。为了完成基于PSI的后处理（如格式化），并提交对文档的修改，在[`PsiDocumentManager`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/platform/core-api/src/com/intellij/psi/PsiDocumentManager.java)实例上调用`doPostponedOperationsAndUnblockDocument()`。
