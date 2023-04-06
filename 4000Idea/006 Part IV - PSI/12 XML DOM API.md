# XML DOM API

&emsp;&emsp;本文旨在为编写自定义Web服务器集成或易于XML编辑的UI的插件编写者提供帮助。它介绍了IntelliJ平台中的文档对象模型（DOM）-一种使用DTD或基于模式的XML模型的简单方法。将涵盖以下主题：通过将UI连接到DOM来处理DOM本身（读取/写入标记内容，属性和子标记）以及在UI中进行轻松的XML编辑。

&emsp;&emsp;假定读者熟悉Java、Swing、IntelliJ平台XML PSI（类[`XmlTag`](https://github.com/JetBrains/intellij-community/tree/idea/231.8109.175/xml/xml-psi-api/src/com/intellij/psi/xml/XmlTag.java)、[`XmlFile`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/xml-psi-api/src/com/intellij/psi/xml/XmlFile.java)、[`XmlTagValue`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/xml-psi-api/src/com/intellij/psi/xml/XmlTagValue.java)等）、IntelliJ平台插件开发基础（应用程序和项目组件、文件编辑器）。

### XML PSI与DOM

&emsp;&emsp;那么，如何从IntelliJ平台的插件中操作XML？通常情况下，我们必须采取`XmlFile`，获得其根标签，然后通过路径找到所需的子标签。路径由标签名称组成，每个标签都是一个字符串。到处输入这些是很乏味的，而且容易出错。让我们假设你有以下的XML：

```xml
<root>
  <foo>
    <bar>42</bar>
    <bar>239</bar>
  </foo>
</root>
```

&emsp;&emsp;假设你想读取第二个条形元素的内容，即 "239"。

&emsp;&emsp;像下面这样创建链式调用是不正确的

```java
file.getDocument()
    .getRootTag()
    .findFirstSubTag("foo")
    .findSubTags("bar")[1]
    .getValue()
    .getTrimmedText();
```

&emsp;&emsp;因为这里的每个调用都可能返回`null`。

&emsp;&emsp;所以代码可能会像这样：

```java
XmlFile file = ...;
XmlDocument document = file.getDocument();
if (document != null) {
  XmlTag rootTag = document.getRootTag();
  if (rootTag != null) {
    XmlTag foo = rootTag.findFirstSubTag("foo");
    if (foo != null) {
      XmlTag[] bars = foo.findSubTags("bar");
      if (bars.length > 1) {
        String s = bars[1].getValue().getTrimmedText();
        // do something
      }
    }
  }
}
```

&emsp;&emsp;看起来很糟糕，不是吗？但有一个更好的方法来做同样的事情。你只需要扩展一个特殊的接口--[`DomElement`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/DomElement.java)。

&emsp;&emsp;例如，让我们创建几个接口：

```java
interface Root extends com.intellij.util.xml.DomElement {
  Foo getFoo();
}

interface Foo extends com.intellij.util.xml.DomElement {
  List<Bar> getBars();
}

interface Bar extends com.intellij.util.xml.DomElement {
  String getValue();
}
```

&emsp;&emsp;接下来，您应该创建一个 `DomFileDescription` 类，将根标记名称和根元素接口传递给它的构造函数。使用 `com.intellij.dom.fileMetaData` 扩展点在 [`plugin.xml`](https://plugins.jetbrains.com/docs/intellij/plugin-configuration-file.html) 中注册它，并指定 `rootTagName` 和 `domVersion`/`stubVersion` 属性。

> 当针对2019.1或更早版本时，请使用`com.intellij.dom.fileDescription`扩展点。

&emsp;&emsp;现在可以从[`DomManager`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/DomManager.java)中获取文件元素。要获取“239”值，您只需要编写以下代码：

```java
DomManager manager = DomManager.getDomManager(project);
Root root = manager.getFileElement(file).getRootElement();
List<Bar> bars = root.getFoo().getBars();
if (bars.size() > 1) {
  String s = bars.get(1).getValue();
  // do something
}
```

&emsp;&emsp;我想这看起来更好一些。你常常在不止一个地方使用你的模型。重新创建模型效率太低，所以我们为你将其缓存，并且任何后续对DomManager.getFileElement()的调用都将返回同一实例。因此，只需调用此方法一次，然后在任何地方保留你获取的“根”对象即可。这样，你就不需要重复那可怕的第一行，代码看起来甚至更漂亮。

&emsp;&emsp;同样需要注意的是，在这种情况下我们避免了潜在的`NullPointerException`：我们的DOM保证每个访问标签子元素的方法都将返回一个非空元素，即使相应名称的子标签不存在。这乍一看可能有些奇怪，但它似乎相当方便。它是如何工作的呢？简单。给定那些接口，DOM在运行时生成所有访问正确子标签和创建模型元素所需的代码。子标记名称和元素类型来自方法名称，返回类型和方法注释（如果有）。在大多数情况下，注释可以省略，如我们的示例，但在本文中将进一步讨论。

&emsp;&emsp;现在让我们更彻底地了解DOM可以做什么，看看表示各种XML概念的可能方式，如标记内容、属性或子标记。稍后，我们将讨论使用模型的基本方法，以及涵盖更高级的功能。最后，我们将看到如何轻松地为DOM模型元素创建UI编辑器。

### 构建模型

##### 标签内容

&emsp;&emsp;在XML PSI中，标签的内容被称为标签值，为了保持一致，我们也将使用此术语。要读取和更改标签值，您需要在接口中添加两种方法（getter和setter），如下所示：

```java
String getValue();
void setValue(String s);
```

&emsp;&emsp;这些方法名（getValue和setValue）是标准的，默认情况下用于访问标记值。如果您想为相同的目标使用自定义方法名，您应该使用@TagValue注释这些方法，例如：

```java
@TagValue
String getTagValue();

@TagValue
void setTagValue(String s);
```

正如您所看到的，我们的访问器使用字符串值。这是很自然的，因为XML代表一种文本格式，而标签内容总是文本。但有时您可能希望操作整数、布尔值、枚举甚至类名（它们当然会被表示为`PsiClass`）以及更通用的Java类型（`PsiType`）。在这种情况下，您只需要将方法中的类型更改为需要的类型，一切都将正常工作。

###### 自定义值类型

```text
https://plugins.jetbrains.com/docs/intellij/xml-dom-api.html#custom-value-types
```

