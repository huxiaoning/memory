# 导航PSI

&emsp;&emsp;有三种主要的遍历PSI的方式：自上而下、自下而上和引用。在第一种情况下，您拥有一个PSI文件或另一个较高级别的元素（例如，一个方法），您需要找到所有符合指定条件的元素（例如，所有变量声明）。在第二种情况下，您拥有PSI树中的一个特定点（例如，插入符号所在的元素），并需要查找它的上下文（例如，它被声明在哪个元素中）。最后，引用允许您从一个元素的用法（例如，方法调用）导航到其声明（被调用的方法），并返回。引用在一个[单独的主题](https://plugins.jetbrains.com/docs/intellij/psi-references.html)中进行了描述。

### 自上而下导航

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
