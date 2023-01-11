# 创建Flux或Mono的简单方式并订阅

&emsp;&emsp;开始使用Flux和Mono的最简单方法是使用它们各自类中的众多工厂方法之一。

&emsp;&emsp;例如，要创建一个 String 序列，您可以枚举它们或将它们放在一个集合中并从中创建 Flux，如下所示：

```java
Flux<String> seq1 = Flux.just("foo", "bar", "foobar");

List<String> iterable = Arrays.asList("foo", "bar", "foobar");
Flux<String> seq2 = Flux.fromIterable(iterable);
```

&emsp;&emsp;工厂方法的其他示例：

```java
Mono<String> noData = Mono.empty(); 
Mono<String> data = Mono.just("foo");
Flux<Integer> numbersFromFiveToSeven = Flux.range(5, 3); 
```

&emsp;&emsp;在订阅方面，Flux 和 Mono 使用 Java 8 lambda。您有多种 .subscribe() 变体可供选择，这些变体将 lambda 用于不同的回调组合，如以下方法签名所示：

```java
Disposable subscribe(); // 订阅并触发序列。
Disposable subscribe(Consumer<? super T> consumer); // 对每个产生的值做一些事情。
Disposable subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer);  // 处理值但也对错误做出反应。
Disposable subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer); // 处理值和错误，但也在序列成功完成时运行一些代码。
Disposable subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer,
          Consumer<? super Subscription> subscriptionConsumer); // 处理值和错误并成功完成，但也对该订阅调用产生的订阅做一些事情。
```

&emsp;&emsp;这些变体返回对订阅的引用，您可以在不需要更多数据时使用该引用取消订阅。取消后，源应停止产生值并清理它创建的所有资源。这种取消和清理行为在 Reactor 中由通用的 **Disposable** 接口表示。

