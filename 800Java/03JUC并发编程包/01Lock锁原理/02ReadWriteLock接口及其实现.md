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



### 读写锁解决缓存雪崩的问题

```java
class TeacherInfoCache {
    static volatile boolean cacheValid;
    static final ReadWriteLock rwl = new ReentrantReadWriteLock();

    static Object get(String dataKey) {
        Object data = null;
        rwl.readLock().lock();
        try {
            if (cacheValid) {
                data = Redis.data.get(dataKey);
            } else {
                // 可能会发生缓存雪崩的问题(刚开始缓存不可用，很多线程查库)
                data = DataBase.queryUserInfo();
                Redis.data.put(dataKey, data);
                cacheValid = true;
            }
            return data;
        } finally {
            rwl.readLock().unlock();
        }
    }

    // 查库时加个写锁,并且双重检查
    static Object get2(String dataKey) {
        if (!cacheValid) {
            rwl.writeLock().lock();
            try {
                if (!cacheValid) {
                    Object data = DataBase.queryUserInfo();
                    Redis.data.put(dataKey, data);
                    cacheValid = true;
                    return data;
                }
            } finally {
                rwl.writeLock().unlock();
            }
        }
        rwl.readLock().lock();
        try {
            return Redis.data.get(dataKey);
        } finally {
            rwl.readLock().unlock();
        }
    }
}

class DataBase {
    static String queryUserInfo() {
        System.out.println("查询数据库。。。");
        return "name:Kody,age:40,gender:true";
    }
}

class Redis {
    static Map<String, Object> data = new HashMap<>();
}
```



### 锁降级

指的是写锁降级成为读锁。持有写锁的同时，再获取读锁，随后释放写锁的过程。写锁是线程独占，读锁是共享，所以写—→读是降级。（读＞写，是不能实现的）





手写读写锁

```java
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.atomic.AtomicStampedReference;
import java.util.concurrent.locks.LockSupport;
public class MyReentrantReadWriteLock {

    /**
     * 获取读锁的线程数量
     */
    private final AtomicStampedReference<LockCount> lockCount = new AtomicStampedReference<>(new LockCount(0, 0), 0);

    /**
     * 写锁线程
     */
    private volatile Thread owner;
    /**
     * 等待队列(读写线程都在这里等)
     */
    private final ConcurrentLinkedQueue<WaitNode> waiters = new ConcurrentLinkedQueue<>();


    public void lock() {
        WaitNode waitNode = new WaitNode(false, Thread.currentThread());
        waiters.offer(waitNode);
        for (; ; ) {
            WaitNode head = waiters.peek();
            if (head == null) {
                // 这里不可能为null,若为null,说明有别的线程处理了当前线程
                throw new IllegalMonitorStateException();
            }
            if (head.getThread() != Thread.currentThread()) {
                LockSupport.park();
            }
            boolean success = tryLock(1);
            if (!success) {
                LockSupport.park();
            }
            waiters.poll();
            return;
        }

    }

    public boolean unlock() {
        int arg = 1;
        boolean success = tryUnlock(arg);
        if (!success) {
            return false;
        }
        WaitNode next = waiters.peek();
        if (next != null) {
            LockSupport.unpark(next.getThread());
        }
        return true;
    }

    public boolean tryLock(int acquires) {
        LockCount oldLockCount = lockCount.getReference();
        int oldStamp = lockCount.getStamp();
        if (oldLockCount.getReadCount() != 0) {
            // 有读锁占用
            return false;
        }
        int writeCount = oldLockCount.getWriteCount();
        if (writeCount == 0) {
            boolean success = lockCount.compareAndSet(oldLockCount, new LockCount(0, acquires), oldStamp, oldStamp + 1);
            if (success) {
                owner = Thread.currentThread();
                return true;
            } else {
                return false;
            }
        } else {
            if (owner == Thread.currentThread()) {
                lockCount.set(new LockCount(0, writeCount + acquires), oldStamp + 1);
                return true;
            } else {
                return false;
            }
        }
    }

    public boolean tryUnlock(int releases) {
        if (owner != Thread.currentThread()) {
            throw new IllegalMonitorStateException();
        }
        LockCount oldLockCount = lockCount.getReference();
        int oldStamp = lockCount.getStamp();
        int nextWriteCount = oldLockCount.getWriteCount() - releases;
        if (nextWriteCount < 0) {
            throw new IllegalMonitorStateException();
        }
        lockCount.set(new LockCount(0, nextWriteCount), oldStamp + 1);
        if (nextWriteCount == 0) {
            owner = null;
            return true;
        } else {
            return false;
        }
    }

    public void lockShared() {
        int arg = 1;
        WaitNode waitNode = new WaitNode(true, Thread.currentThread());
        waiters.offer(waitNode);
        for (; ; ) {
            WaitNode head = waiters.peek();
            if (head == null) {
                // 这里不可能为null,若为null,说明有别的线程处理了当前线程
                throw new IllegalMonitorStateException();
            }
            if (head.getThread() != Thread.currentThread()) {
                LockSupport.park();
            }
            boolean success = tryLockShared(arg);
            if (!success) {
                LockSupport.park();
            }
            waiters.poll();
            WaitNode next = waiters.peek();
            if (next != null && next.isRead()) {
                LockSupport.unpark(next.getThread());
            }
            return;
        }
    }

    public boolean unlockShared() {
        int arg = 1;
        boolean success = tryUnlockShared(arg);
        if (!success) {
            return false;
        }
        WaitNode next = waiters.peek();
        if (next != null) {
            LockSupport.unpark(next.getThread());
        }
        return true;
    }

    public boolean tryLockShared(int acquires) {
        for (; ; ) {
            LockCount oldLockCount = lockCount.getReference();
            int oldStamp = lockCount.getStamp();
            int writeCount = oldLockCount.getWriteCount();
            if (writeCount > 0 && owner != Thread.currentThread()) {
                return false;
            }
            int readCount = oldLockCount.getReadCount();
            boolean success = lockCount.compareAndSet(oldLockCount, new LockCount(readCount + acquires, writeCount), oldStamp, oldStamp + 1);
            if (success) {
                return true;
            }
        }
    }

    public boolean tryUnlockShared(int releases) {
        for (; ; ) {
            LockCount oldLockCount = lockCount.getReference();
            int oldStamp = lockCount.getStamp();
            int nextReadCount = oldLockCount.getReadCount() - releases;
            if (nextReadCount < 0) {
                throw new IllegalMonitorStateException();
            }
            if (lockCount.compareAndSet(oldLockCount, new LockCount(nextReadCount, oldLockCount.getWriteCount()), oldStamp, oldStamp + 1)) {
                return nextReadCount == 0;
            }
        }
    }
}
```



70:47
