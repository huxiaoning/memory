# Queue

队列:先进先出。



### Queue API

| 方法    | 作用                     | 描述                                               | 阻塞 |
| ------- | ------------------------ | -------------------------------------------------- | ---- |
| add     | 增加一个元素             | 如果队列已满，则抛出一个IllegalSlabEepeplian异常   | 异常 |
| remove  | 移除并返回队列头部的元素 | 如果队列为空，则抛出一个NoSuchElementException异常 | 异常 |
| element | 返回队列头部的元素       | 如果队列为空，则抛出一个NoSuchElementException异常 | 异常 |
| offer   | 添加一个元素并返回true   | 如果队列已满，则返回false                          |      |
| poll    | 移除并返问队列头部的元素 | 如果队列为空，则返回null                           |      |
| peek    | 返回队列头部的元素       | 如果队列为空，则返回null                           |      |
| put     | 添加一个元素             | 如果队列满，则阻塞                                 | 阻塞 |
| take    | 移除并返回队列头部元素   | 如果队列空，则阻塞                                 | 阻塞 |



### ArrayBlockingQueue

存储结构为数组。

线程安全。

构造函数要求指定容量，且不能扩容（如果能扩容还阻塞个什么劲）。

有界。



### LinkedBlockingDeque

存储结构为链表。

线程安全。

构造时可以指定容量，也可以不指定。不指定时表示容量无限大，Integer.MAX_VALUE。

可以定义为有界或无界队列。

并发度高于ArrayBlockingQueue。



### ConcurrentLinkedQueue

存储结构为链表。

线程安全。

非阻塞，无界。

并发性能更高。



### SynchronousQueue

阻塞队列，没有容量。

take时会阻塞，直到有其他线程put/offer一个元素到队列。

put时也会阻塞，直到有其他线程take/poll走一个元素。

若没有线程执行take阻塞，offer新增元素会失败，返回false。

若没有线程执行put阻塞，pool会取到一个null。

peek取到的元素永远为null。

同步队列(线程安全)。



### PriorityBlockingQueue

存储结构为数组。默认容量是11。会扩容，无界队列。

还可以指定一个比较器，使其成为一个有序(由Comparator指定)的队列。

如果队列泛型指定为String或Integer这样的实现了Comparable接口的类，队列的顺序由compareTo方法决定。

