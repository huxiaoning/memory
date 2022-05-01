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

同步队列(线程安全)。

