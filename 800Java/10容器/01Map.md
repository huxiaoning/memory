# Map



### HashMap1.7

存储结构：数组+链表。

数组中的每一个元素都是链表。

### HashMap1.8

在1.7的基础上链表可升级为红黑树，加速检索。

### ConcurrentHashMap1.7

存储结构：数组+段。

数组中的第一个元素都是一个段对象。

每个段对象都是一个定制和HashTable。HashTable的每个方法都是synchronized修饰的。

分段琐。

### ConcurrentHashMap1.8

与1.7不同。

存储结构：数组+链表。

链表可升级为红黑树。

并发度高于1.7。



### ConcurrentSkipListMap

key有序。

