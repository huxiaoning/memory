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

&emsp;&emsp;正如您所看到的，我们的访问器使用字符串值。这是很自然的，因为XML代表一种文本格式，而标签内容总是文本。但有时您可能希望操作整数、布尔值、枚举甚至类名（它们当然会被表示为`PsiClass`）以及更通用的Java类型（`PsiType`）。在这种情况下，您只需要将方法中的类型更改为需要的类型，一切都将正常工作。

###### 自定义值类型

&emsp;&emsp;如果您使用更加奇特的类型，那么您应该告诉DOM如何处理它们。首先，在访问器方法上注释`@Convert`注解，并在注解中指定自己的类，该类应扩展`Converter<T>`类。这里T是您的奇特类型，而`Converter<T>`是一种知道如何在`String`和`T`之间转换值的东西。如果无法转换值（例如，“foo”不能转换为`Integer`），则转换器可能返回`null`。还请注意，您的实现应具有无参数构造函数。

&emsp;&emsp;让我们考虑一个有趣的情况，当`T`表示枚举值时。通常，转换器只是在XML中搜索指定名称的枚举元素。但有时候，为了它们的名称，您可能需要或想要使用不是有效Java标识符的值。例如，在EJB中CMP版本可以是“1.x”或“2.x”，但您无法创建具有这些名称的Java枚举。对于这种情况，请让您的枚举实现`NamedEnum`接口，然后按照您希望命名您的枚举元素。现在，只需提供`getValue()`实现即可返回正确值以与XML内容匹配，并完成！在我们的示例中，代码将如下所示：

```java
enum CmpVersion implements NamedEnum {
  CmpVersion_1_X ("1.x"),
  CmpVersion_2_X ("2.x");
  private final String value;
  CmpVersion(String value) {
    this.value = value;
  }
  public String getValue() {
    return value;
  }
}
```

&emsp;&emsp;正如我们已经提到的，一个XML标签除了它的值之外可能还有很多附属信息：可以有属性、子元素，但通常（例如根据DTD或Schema）它只应该具有值。当然，这样的标签也需要与DOM元素相关联。我们提供了这样一个元素：

```java
interface GenericDomValue<T> {
  T getValue();
  void setValue(T t);
  @TagValue
  String getStringValue();
  @TagValue
  void setStringValue(String s);
}
```

&emsp;&emsp;因此，当使用此接口时，您可以指定特定的`T` - 一切都将正常工作。提供处理字符串的方法有很多原因。例如，如果您的`T`是`PsiClass`，则在UI中突出显示无效值会非常有用。为了获取要突出显示的值（来自XML文件的字符串），我们有`getStringValue()`方法。错误消息将通过`getErrorMessage()`从转换器中获取。

##### 属性

&emsp;&emsp;属性也相对简单易处理。您可以读取它们的值，设置它们，并使用不同类型进行操作。因此，自然而然地创建类似于`GenericDomValue<T>`这样的东西，然后像往常一样工作。“类似于”将成为一个继承者，如下所示：

```java
interface GenericAttributeValue<T> extends GenericDomValue<T> {
  XmlAttribute getXmlAttribute();
}
```

&emsp;&emsp;考虑您想要使用名为“some-class”的属性，其值为`PsiClass`类型：

```java
@Attribute("some-class")
GenericAttributeValue<PsiClass> getMyAttributeValue();
```

&emsp;&emsp;就是这样！现在你可以获取/设置值，解析此`PsiClass`，获取其字符串表示等。属性的名称将从方法名中取得（请参见下一段）。如果您以特殊方式命名方法，则甚至可以省略注释。例如：

```java
GenericAttributeValue<PsiClass> getSomeClass();
```

&emsp;&emsp;[`DomNameStrategy`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/DomNameStrategy.java)接口指定了如何将访问器名称转换为XML元素名称。更确切地说，不是完整的访问器名称，而是减去任何“get”、“set”或“is”前缀的名称。策略类在任何DOM元素接口中的`@NameStrategy`注释中指定。然后，该接口的任何后代和子级都将使用此策略。默认策略是[`HyphenNameStrategy`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/HyphenNameStrategy.java)，在其中单词由连字符分隔（请参见上面的示例）。另一个常见变体是[`JavaNameStrategy`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/JavaNameStrategy.java)，它大写每个单词的第一个字母，就像Java命名约定一样。在我们的示例中，属性名称将为“someClass”。

&emsp;&emsp;如果属性没有定义为`PsiClass`，而是其他需要转换器的自定义`T`类型，则只需在getter方法上指定`@Convert`注解即可。

&emsp;&emsp;请注意，即使在XML中未指定属性，其getter方法也永远不会返回`null`。它的`getValue()`、`getStringValue()`和`getXmlAttribute()`方法将返回`null`，但DOM接口实例将存在且有效。如果元素具有基础属性，则可以轻松解决此问题（当然，仅在需要时才能这样做）：只需调用`undefine()`方法（定义在`DomElement`中），XML属性就会消失，而[`GenericAttributeValue`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/GenericAttributeValue.java)仍然有效。

##### 子标签：固定数量
&emsp;&emsp;您可能经常处理具有最多一个给定名称的子标记（例如`<ejb-name>`，`<ejb-class>`或`<cmp-field>`）的标记，这些标记定义实体EJB。要使用此类子项，请为其提供getter。这些getter应该具有扩展`DomElement`的返回类型：

```java
GenericDomValue<String> getEjbName();
GenericDomValue<String> getEjbClass();
CmpField getCmpField();
```

&emsp;&emsp;还有一种注释可以明确指定这样的子元素：`@SubTag`。它的"value"属性包含一个标签名称。如果没有指定，将使用当前命名策略从方法名称中推断出名称。

&emsp;&emsp;有时候，子标签的存在意味着某些东西，而不是它的内容 - 例如，在 EJB 方法权限中的 `<unchecked>` 标记。如果存在，则权限未经检查，否则已经检查。对于这种情况，应该创建一个特殊的 `GenericDomValue<Boolean>` 子元素。通常情况下，如果标记值中有 "true" 则 `getValue()` 返回 `true` ，如果标记值中有 "false" 则返回 `false` ，否则返回 `null` 。在 `@SubTag` 注释中，可以指定属性如 `indicator=true` 。在这种情况下，`getValue()` 将在标签存在时返回 `true` ，否则返回 `false` 。

&emsp;&emsp;让我们考虑另一个受EJB启发的有趣例子，其中存在具有两个角色的关系，每个角色指定一个关系端点：第一角色和第二角色。它们都由具有相同值的标签表示。因此，我们可以创建一个包含多个角色元素的集合，并且每次访问某些角色时，我们将检查该集合是否具有足够数量的元素。但是DOM的主要目的之一是消除不必要的检查。那么为什么不能拥有带有相同标记名称的固定（超过一个）数量子项呢？让我们来试试！

```java
@SubTag(value = "ejb-relationship-role", index = 0)
EjbRelationshipRole getEjbRelationshipRole1();

@SubTag(value = "ejb-relationship-role", index = 1)
EjbRelationshipRole getEjbRelationshipRole2();
```

&emsp;&emsp;第一种方法将返回第一个名为`<ejb-relationship-role>`的子标记的DOM元素，第二个方法将返回第二个。因此，对于这些子项，“固定数量”是一个术语。根据DTD或模式，应该有给定名称的子标记的固定数量。大多数情况下，这个固定数字是1；在我们与关系相关的情况下，则为2。就像属性一样，无论底层标签是否存在，都存在具有固定数量的子项。如果您需要删除标记，则可以使用相同的`undefine()`方法来完成。

&emsp;&emsp;对于 [`GenericDomValue`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/GenericDomValue.java) 类型的子元素，您也可以像属性一样指定转换器。

##### 子标签：收藏

&emsp;&emsp;DTD和Schemas中的另一个常见情况是，当子元素具有相同的标记名称和非固定上限计数时。它们的访问器与固定数量子元素不同，如下所示：返回结果是扩展了`DomElement`特殊类型的`Collection`或`List`，并且如果您想使用名称策略，则方法名必须为复数形式。例如，在EJB中我们将有以下方法：

```java
List<Entity> getEntities();
```

&emsp;&emsp;还有一个注释`@SubTagList`，您可以明确指定标签名称。

&emsp;&emsp;返回的集合不能直接修改。要从集合中删除一个元素，只需在该元素上调用`undefine()`即可。标签将被删除，元素将变为无效（`DomElement.isValid() == false`）。请注意，此行为与固定数量的子项和属性不同：它们始终有效，即使在`undefine()`之后也是如此。同样，与这些子类型不同，集合子始终具有有效的基础XML标记。

&emsp;&emsp;添加元素有点困难。由于所有的DOM元素都是在内部创建的，你不能仅仅将一些DOM元素传递给某个方法来添加到集合中。实际上，你必须要求父元素将子元素添加到集合中。在我们的例子中，它是这样完成的：

```java
Entity addEntity(int index);
```

&emsp;&emsp;它可以在任何你想要的地方添加一个元素，或者

```java
Entity addEntity();
```

&emsp;&emsp;这会向集合末尾添加一个新的DOM元素。请注意“`Entity`”一词的单数形式。因为在这里我们处理一个实体对象，而在集合getter中，我们可能处理多个实体。

&emsp;&emsp;现在，您可以对返回的值进行任何操作：修改、定义标签的值、子元素等。

&emsp;&emsp;最后一个常见情况也是一个集合，但它由任意混合的具有不同名称的标签组成。要处理它，您应该为混合集合中所有标签名称定义集合getter，然后定义一个额外的特殊注释getter：

```java
// <foo> elements
List<Foo> getFoos();
// <bar> elements
List<Bar> getBars();
// all <foo> and <bar> elements
@SubTagsList({ "foo", "bar" })
List<FooBar> getMergedListOfFoosAndBars();
```

&emsp;&emsp;这里的注释是必需的 - 我们无法从一个方法名猜测出多个标签名称。

&emsp;&emsp;要向这样的混合集合中添加元素，您应该为每个可能的标签名称创建“add”方法：

```java
@SubTagsList(value = { "foo", "bar" }, tagName = "foo")
FooBar addFoo();
@SubTagsList(value = { "foo", "bar" }, tagName = "bar")
FooBar addBar(int index);
```

&emsp;&emsp;在最后一个例子中，索引参数表示合并集合中的索引，而不是名为“bar”的标签集合中的索引。

##### 动态定义

&emsp;&emsp;您可以通过实现 `com.intellij.util.xml.reflect.DomExtender<T>` 在运行时扩展现有的 DOM 模型。在 `com.intellij.dom.extender` 扩展点的 "extenderClass" 属性中注册它，其中 "domClass" 指定要扩展的 DOM 类 `<T>`。[`DomExtensionsRegistrar`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/reflect/DomExtensionsRegistrar.java) 提供了各种方法来注册动态属性和子元素。

&emsp;&emsp;如果贡献的元素依赖于除纯 XML 文件内容之外的任何东西（使用框架版本、类路径中的库等），请确保从 `DomExtender.supportsStubs()` 返回 `false`。

##### 命名空间支持

&emsp;&emsp;使用`DomFileDescription.registerNamespacePolicy()`在DOM模型中注释[`NameSpace`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/Namespace.java)，并注册命名空间键映射，从而通过`DomFileDescription.initializeFileDescription()`初始化文件描述。

##### IDE 支持

&emsp;&emsp;插件 DevKit 支持以下功能，用于处理与 DOM 相关的代码：

- [`DomElement`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/DomElement.java) - 为继承类中定义的所有与 DOM 相关的方法提供隐式用法（以抑制“未使用方法”警告）。
- [`DomElementVisitor`](https://github.com/JetBrains/intellij-community/blob/idea/231.8109.175/xml/dom-openapi/src/com/intellij/util/xml/DomElementVisitor.java) - 为继承类中定义的所有与DOM相关的访问器方法提供隐式用法（以抑制“未使用方法”警告）

### 使用DOM操作
