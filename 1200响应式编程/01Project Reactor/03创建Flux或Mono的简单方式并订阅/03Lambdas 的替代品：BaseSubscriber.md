# Lambdas 的替代品：BaseSubscriber

&emsp;&emsp;还有一个更通用的附加订阅方法，它采用一个成熟的订阅者，而不是从 lambda 中组合一个。为了帮助编写这样的订阅者，我们提供了一个名为 BaseSubscriber 的可扩展类。

&emsp;&emsp;BaseSubscriber 的实例（或其子类）是一次性的，这意味着如果 BaseSubscriber 订阅了第二个发布者，它会取消对第一个发布者的订阅。这是因为两次使用一个实例会违反 Reactive Streams 的规则，即不得并行调用订阅者的 onNext 方法。因此，匿名实现只有在对 Publisher#subscribe(Subscriber) 的调用中直接声明时才适用。

&emsp;&emsp;现在我们可以实现其中之一。我们称之为 SampleSubscriber。以下示例显示了如何将其附加到 Flux：

```java
SampleSubscriber<Integer> ss = new SampleSubscriber<Integer>();
Flux<Integer> ints = Flux.range(1, 4);
ints.subscribe(ss);
```

&emsp;&emsp;以下示例显示了 SampleSubscriber 的外观，作为 BaseSubscriber 的简约实现：

```java
package io.projectreactor.samples;
import org.reactivestreams.Subscription;
import reactor.core.publisher.BaseSubscriber;
public class SampleSubscriber<T> extends BaseSubscriber<T> {
	public void hookOnSubscribe(Subscription subscription) {
		System.out.println("Subscribed");
		request(1);
	}
	public void hookOnNext(T value) {
		System.out.println(value);
		request(1);
	}
    protected void hookOnCancel() {
        System.out.println("hookOnCancel");
    }
    protected void hookOnComplete() {
        System.out.println("hookOnComplete");
    }
    protected void hookFinally(SignalType type) {
        System.out.println(type.toString());
    }
}
```

&emsp;&emsp;SampleSubscriber 类扩展了 BaseSubscriber，这是推荐用于 Reactor 中用户自定义订阅者的抽象类。该类提供了可以被覆盖以调整订阅者行为的挂钩。默认情况下，它会触发一个无限制的请求，其行为与 subscribe() 完全相同。但是，当您需要自定义请求量时，扩展 BaseSubscriber 会更有用。

&emsp;&emsp;对于自定义请求量，最低限度是实现 hookOnSubscribe(Subscription subscription) 和 hookOnNext(T value)，就像我们所做的那样。在我们的例子中，hookOnSubscribe 方法将一条语句打印到标准输出并发出第一个请求。然后 hookOnNext 方法打印一条语句并执行其他请求，一次一个请求。

&emsp;&emsp;SampleSubscriber 类产生以下输出：

```bash
Subscribed
1
2
3
4
```

&emsp;&emsp;BaseSubscriber 还提供了一个 requestUnbounded() 方法来切换到无界模式（相当于 request(Long.MAX_VALUE)），以及一个 cancel() 方法。

&emsp;&emsp;它还具有额外的挂钩：hookOnComplete、hookOnError、hookOnCancel 和 hookFinally（在序列终止时始终调用，终止类型作为 SignalType 参数传入）

&emsp;&emsp;您几乎肯定想要实现 hookOnError、hookOnCancel 和 hookOnComplete 方法。您可能还想实现 hookFinally 方法。SampleSubscriber 是执行有界请求的 Subscriber 的绝对最小实现。

