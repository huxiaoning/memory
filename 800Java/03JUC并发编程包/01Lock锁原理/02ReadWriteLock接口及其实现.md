# ReadWriteLock接口及其实现

### ReadWriteLock API

| 方法签名            | 描述     |
| ------------------- | -------- |
| `Lock readLock();`  | 获取读锁 |
| `Lock writeLock();` | 获取写锁 |

```java
    public static void main(String[] args) {
        ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
        System.out.println(readWriteLock.readLock() == readWriteLock.readLock()); // true
        System.out.println(readWriteLock.writeLock() == readWriteLock.writeLock()); // true
    }
```

##### ReadWriteLock概念

维护一对关联锁，一个只用于读操作，一个只用于写操作；

读锁可以由多个读线程同时持有，写锁是排他的。同一时间，两把锁不能被不同线程持有。

##### ReadWriteLock适用场景

适合读取操作多于写入操作的场景，改进互斥锁的性能，比如：集合的并发线程安全性改造、缓存组件。

##### 读写锁只能锁降级，不能锁升级

- 可以锁降级
  - 持有写锁的线程，不需要释放写锁，可以直接获取到读锁
- 不能锁升级
  - 持有读锁的线程，必须释放读锁，才可以抢写锁。
  - 如果不释放读锁，就去抢写锁，将永久阻塞，陷入死锁。



```java
public class ReadWriteLockApi {

    public static void main(String[] args) {
        lockCanDowngrade();
        lockCanNotUpgrade();
    }

    public static void lockCanDowngrade() {
        ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
        readWriteLock.writeLock().lock();
        readWriteLock.readLock().lock();
        System.out.println("锁降级，没毛病。");
        readWriteLock.readLock().unlock();
        readWriteLock.writeLock().unlock();
    }

    public static void lockCanNotUpgrade() {
        ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
        readWriteLock.readLock().lock();
        readWriteLock.writeLock().lock(); // 死锁，这里永远获取不到写锁,必须释放上面的读锁才行
        System.out.println("锁升级，做不到。");
        readWriteLock.writeLock().unlock();
        readWriteLock.readLock().unlock();
    }
}
```



##### 读写锁实现原理

```java
public class MyReentrantReadWriteLock {

    /**
     * 获取读锁的线程数量
     */
    private AtomicInteger readCount = new AtomicInteger();
    /**
     * 写锁重入次数
     */
    private AtomicInteger writeCount = new AtomicInteger();

    /**
     * 写锁线程
     */
    private volatile Thread owner;
    /**
     * 等待队列
     */
    private final ConcurrentLinkedQueue<Thread> waiters = new ConcurrentLinkedQueue<>();
}
```



### 用读写锁将HashMap改造成并发安全的容器

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class MyHashMap<K, V> {

    private final Map<K, V> m = new HashMap<>();

    private final ReadWriteLock rwl = new ReentrantReadWriteLock();
    private final Lock r = rwl.readLock();
    private final Lock w = rwl.writeLock();


    public V get(K k) {
        r.lock();
        try {
            return m.get(k);
        } finally {
            r.unlock();
        }
    }

    public K[] allKeys() {
        r.lock();
        try {
            return (K[]) m.keySet().toArray();
        } finally {
            r.unlock();
        }
    }

    public V put(K k, V v) {
        w.lock();
        try {
            return m.put(k, v);
        } finally {
            w.unlock();
        }
    }

    public void clear() {
        w.lock();
        try {
            m.clear();
        } finally {
            w.unlock();
        }
    }

}
```

11:03
