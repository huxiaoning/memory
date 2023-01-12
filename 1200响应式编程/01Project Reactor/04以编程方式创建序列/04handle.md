# Handle

&emsp;&emsp;handle 方法有点不同：它是一个实例方法，这意味着它被链接到一个现有的源上（就像常见的操作符一样）。它存在于 Mono 和 Flux 中。

&emsp;&emsp;它接近generate，因为它使用 SynchronousSink 并且只允许一个接一个的发射。但是，handle可用于从每个源元素生成任意值，可能会跳过一些元素。这样，它就可以作为map和filter的组合。handle的签名如下：

```java
Flux<R> handle(BiConsumer<T, SynchronousSink<R>>);
```

&emsp;&emsp;让我们考虑一个例子。反应流规范不允许在序列中使用空值。如果你想执行一个 map 但你想使用一个预先存在的方法作为 map 函数，而那个方法有时会返回 null 怎么办？

&emsp;&emsp;例如，以下方法可以安全地应用于整数源：

```java
public String alphabet(int letterNumber) {
	if (letterNumber < 1 || letterNumber > 26) {
		return null;
	}
	int letterIndexAscii = 'A' + letterNumber - 1;
	return "" + (char) letterIndexAscii;
}
```

&emsp;&emsp;然后我们可以使用 handle 删除任何空值：

&emsp;&emsp;示例 13. 将handle用于“映射并消除空值”场景

```java
Flux<String> alphabet = Flux.just(-1, 30, 13, 9, 20)
    .handle((i, sink) -> {
        String letter = alphabet(i);  // 1 映射为字母。
        if (letter != null)  // 2 如果“map函数”返回null…。
            sink.next(letter); // 3 通过不调用 sink.next 来过滤掉它。
    });
alphabet.subscribe(System.out::println);
```

&emsp;&emsp;这将打印出：

```bash
M
I
T
```

