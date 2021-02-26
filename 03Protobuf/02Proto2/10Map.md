## `Map`

如果要创建关联映射作为数据定义的一部分，协议缓冲区提供了一种方便的快捷语法:

```protobuf
map<key_type, value_type> map_field = N;
```

其中键类型可以是任何整数或字符串类型。（除了浮点类型和字节之外的任何标量类型）。

枚举不能做为`key`。

值可以是任何类型，除了map类型。

因此，例如，如果您想创建一个`Project`映射，其中每个`Project`消息都与一个字符串键相关联，您可以这样定义它：

```protobuf
map<string, Project> projects = 3;
```

生成的`map` `API`当前可用于所有`proto2`支持的语言。您可以在相关API参考中找到有关所选语言的`map API`的更多信息。

### `Map`特征

- map不支持扩展。
- map不能用`repeated`, `optional`, or `required`修饰。
- 映射值的Wire格式排序和映射迭代排序未定义，因此不能依赖特定顺序的映射项。
- 为`.proto`生成文本格式时，`map`按`key`排序。数字`key`按数字排序。
- 从关联进行分析或合并时，如果存在重复的映射键，则使用最后看到的键。从文本格式解析映射时，如果存在重复键，则解析可能会失败。

### 向后兼容

`map`语句与下面的方式相同，因此，不支持`map`的协议缓冲区实现仍然可以处理您的数据：

```protobuf
message MapFieldEntry {
  optional key_type key = 1;
  optional value_type value = 2;
}

repeated MapFieldEntry map_field = N;
```

任何支持映射的协议缓冲区实现都必须生成和接受上述定义可以接受的数据。

