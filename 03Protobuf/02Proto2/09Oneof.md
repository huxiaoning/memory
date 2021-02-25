## `Oneof`

如果您有一个包含许多可选`optional`字段的消息，并且最多只能同时设置一个字段，则可以使用`oneof`功能强制执行此行为并节省内存。

`oneof`字段与`optional`字段类似，只是`oneof`字段中的所有字段共享内存，并且同时最多可以设置一个字段的值。

设置`oneof`的任何成员将自动清除所有其他成员。

您可以使用特殊的`case（）`或`WhichOneof（）`方法检查`oneof`中设置了哪个值（如果有的话），具体取决于您选择的语言。

### 使用`oneof`

定义一个`oneof`字段:

```protobuf
message SampleMessage {
  oneof test_oneof {
     string name = 4;
     SubMessage sub_message = 9;
  }
}
```

然后将`oneof`字段添加到`oneof` 定义。您可以添加任何类型的字段，但不能使用`required`、`optional`或`repeated`关键字。如果需要向`oneof`添加`repeated`字段，可以使用包含`repeated`字段的消息。

在生成的代码中，`oneof`字段具有与常规`optional`相同的`getter`和`setter`方法。您还可以使用一种特殊的方法来检查`oneof`中设置了哪个值（如果有的话）。您可以在相关的API参考中找到有关所选语言的`oneof`API的更多信息。

### `oneof`特征

- 设置`oneof`字段将自动清除`oneof`的所有其他成员。因此，如果您设置了`oneof`中多个字段的值，那么只有最后设置的字段仍然有值。

  ```c++
  SampleMessage message;
  message.set_name("name");
  CHECK(message.has_name());
  message.mutable_sub_message();   // Will clear name field.
  CHECK(!message.has_name());
  ```

  

- 如果解析器在`wire`上遇到同一个`oneof`的多个成员，则在解析的消息中只使用最后一个看到的成员。

- 扩展不支持`oneof`。

- `oneof`不能是`repeated`的。

- 反射`API`适用于`oneof`字段。

- 如果将`oneof`字段设置为默认值（例如将`int32 `的`oneof`字段设置为0），则将设置该`oneof`字段的“case”，并且该值将在连线上序列化。

- 如果使用C++，请确保代码不会导致内存崩溃。下面的示例代码将崩溃，因为通过调用`set_name（）`方法已经删除了`sub_message`。

  ```c++
  SampleMessage message;
  SubMessage* sub_message = message.mutable_sub_message();
  message.set_name("name");      // Will delete sub_message
  sub_message->set_...            // Crashes here
  ```

  

- 还是在`C++`中，如果你交换两个`oneof`消息，每条消息的结尾都是另一条的`one of`,在下面的例子中，`msg1`会拥有`sub_message`,而`msg2`会拥有`name`。

  ```c++
  SampleMessage msg1;
  msg1.set_name("name");
  SampleMessage msg2;
  msg2.mutable_sub_message();
  msg1.swap(&msg2);
  CHECK(msg1.has_sub_message());
  CHECK(msg2.has_name());
  ```

### 向后兼容性问题

