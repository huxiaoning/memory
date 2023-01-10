# Reactor 核心特性

​	Reactor 项目的主要工件是`reactor-core`，一个专注于 Reactive Streams 规范并以 Java 8 为目标的反应式库。

- **Flux**
  - 包含0~N个元素的流
- **Mono**
  - 包含0~1个元素的流



Mono对象没有count()操作，而Flux对象可以使用count()操作，并返回一个Mono对象。

```java
        Flux<Integer> flux = Flux.just(1, 2, 3);
        Mono<Long> count = flux.count();
```

