# 导航PSI

&emsp;&emsp;有三种主要的遍历PSI的方式：自上而下、自下而上和引用。在第一种情况下，您拥有一个PSI文件或另一个较高级别的元素（例如，一个方法），您需要找到所有符合指定条件的元素（例如，所有变量声明）。在第二种情况下，您拥有PSI树中的一个特定点（例如，插入符号所在的元素），并需要查找它的上下文（例如，它被声明在哪个元素中）。最后，引用允许您从一个元素的用法（例如，方法调用）导航到其声明（被调用的方法），并返回。引用在一个[单独的主题](https://plugins.jetbrains.com/docs/intellij/psi-references.html)中进行了描述。

### 自顶而下导航

&emsp;&emsp;使用 [`Visitor`](https://en.wikipedia.org/wiki/Visitor_pattern) 是进行自顶向下导航的最常见方法。使用 `Visitor`，您需要创建一个类（通常是一个匿名内部类），该类继承基础 `Visitor` 类，重写处理您感兴趣的元素的方法，并将访问者实例传递给 `PsiElement.accept()`。

&emsp;&emsp;访问者的基类是特定于语言的。例如，如果您需要处理Java文件中的元素，则可以扩展[JavaRecursiveElementVisitor](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/java/java-psi-api/src/com/intellij/psi/JavaRecursiveElementVisitor.java)并覆盖对应于您感兴趣的Java元素类型的方法。

&emsp;&emsp;以下代码片段展示了使用 Visitor 查找所有 Java 局部变量声明的示例：

```java
file.accept(new JavaRecursiveElementVisitor() {
  @Override
  public void visitLocalVariable(PsiLocalVariable variable) {
    super.visitLocalVariable(variable);
    System.out.println("Found a variable at offset " +
         variable.getTextRange().getStartOffset());
  }
});
```

(无法导入类JavaRecursiveElementVisitor)

&emsp;&emsp;在许多情况下，您也可以使用更具体的API进行自上而下的导航。例如，如果您需要获取Java类中所有方法的列表，可以使用访问者，但更简单的方法是调用`PsiClass.getMethods()`。

&emsp;&emsp;PsiTreeUtil包含许多通用的、与语言无关的函数，用于PSI树的导航，其中一些函数（例如findChildrenOfType()）执行自顶向下的导航。

### 自低而上导航

&emsp;&emsp;自下而上导航的起点可以是 PSI 树中的特定元素（例如，解析引用后得到的结果）或偏移量。如果您有一个偏移量，则可以通过调用 `PsiFile.findElementAt()`找到相应的 PSI 元素。该方法返回树最低级别处（例如标识符）的元素，如果要确定更广泛的上下文，则需要向上导航树。

&emsp;&emsp;在大多数情况下，可以通过调用`PsiTreeUtil.getParentOfType()`方法进行自下而上的导航。此方法向上遍历整个树，直到找到您指定类型的元素为止。例如，要查找包含的方法，可以调用`PsiTreeUtil.getParentOfType(element, PsiMethod.class)`。

&emsp;&emsp;在某些情况下，您还可以使用特定的导航方法。例如，要找到包含一个方法的类，可以调用`PsiMethod.getContainingClass()`方法。

&emsp;&emsp;下面的代码片段展示了如何同时使用这些方法：

```java
PsiFile psiFile = anActionEvent.getData(CommonDataKeys.PSI_FILE);
PsiElement element = psiFile.findElementAt(offset);
PsiMethod containingMethod = PsiTreeUtil.getParentOfType(element, PsiMethod.class);
PsiClass containingClass = containingMethod.getContainingClass();
```

&emsp;&emsp;要了解导航是如何实际工作的，请参考[代码示例](https://github.com/JetBrains/intellij-sdk-code-samples/blob/main/psi_demo/src/main/java/org/intellij/sdk/psi/PsiNavigationDemoAction.java)。

