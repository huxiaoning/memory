# Lock接口及其实现

### Lock API

| 方法签名                                                     | 描述               | 结论                                         |
| ------------------------------------------------------------ | ------------------ | -------------------------------------------- |
| `void lock();`                                               | 获取锁（不死不休） | 最常用                                       |
| `boolean tryLock();`                                         | 获取锁（浅尝辄止） |                                              |
| `boolean tryLock(long time, TimeUnit unit) throws InterruptedException;` | 获取锁（过时不候） |                                              |
| `void lockInterruptibly() throws InterruptedException;`      | 获取锁（任人摆布） | 一般更昂贵，有的实现类可能没有实现这个方法。 |
| `void unlock();`                                             | 释放锁             |                                              |
| `Condition newCondition();`                                  |                    |                                              |



##### JDK实现`java.util.concurrent.locks.ReentrantLock`

```java
        Lock lock = new ReentrantLock();
        lock.lock();
        try {
            // 要同步的代码块
        } finally {
            lock.unlock();
        }
```

##### Condition

Object中的wait()，notify()，notifyAll()方法是和synchronized配合使用的，可以唤醒一个或者全部（单个等待集）； 

Condition是需要与Lock配合使用的，提供多个等待集合，更精确的控制（底层是park/unpark机制）；

| 协作方式       | 死锁方式1(锁) | 死锁方式2(先唤醒，再挂起) | 备注                        |
| -------------- | ------------- | ------------------------- | --------------------------- |
| suspend/resume | 死锁          | 死锁                      | 弃用                        |
| wait/notify    | 不死锁        | 死锁                      | 只用于synchronized          |
| park/unpark    | 死锁          | 不死锁                    |                             |
| condition      | 不死锁        | 死锁                      | 只用于创建此Condition的Lock |

synchronized只有一个等待队列，也就是监视器锁对象的waitSet,只要调用锁对象的wait方法，当前线程就会进入waitSet,然后由其他线程调用锁对象的notify方法唤醒。

但是Lock却可以有多个等待队列，因为我们可以创建多个condition。

```java
        Lock lock = new ReentrantLock();
        Condition putCondition = lock.newCondition();
        Condition takeCondition = lock.newCondition();
```

可以使用两个condtion实现阻塞队列:

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class MyBlockQueue<E> {

    private final List<E> elementList = new ArrayList<>();

    private final int length;

    private final Lock lock = new ReentrantLock();

    private final Condition putCondition = lock.newCondition();

    private final Condition takeCondition = lock.newCondition();

    public MyBlockQueue(int length) {
        this.length = length;
    }

    public MyBlockQueue() {
        this(16);
    }

    public void put(E e) {
        lock.lock();
        try {
            while (elementList.size() >= length) {
                try {
                    putCondition.await();
                } catch (InterruptedException ex) {
                    ex.printStackTrace();
                }
            }
            elementList.add(e);
            takeCondition.signal();
        } finally {
            lock.unlock();
        }
    }

    public E take() {
        lock.lock();
        try {
            while (elementList.size() <= 0) {
                try {
                    takeCondition.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            E element = elementList.remove(0);
            putCondition.signal();
            return element;
        } finally {
            lock.unlock();
        }
    }
}
```

`ReentrantLock`是一把可重入锁:

```java
    public static void main(String[] args) throws InterruptedException {
        Lock lock = new ReentrantLock(); // owner = null、count = 0
        lock.lock(); // owner = mainThread、count = 1
        lock.lock();// owner = mainThread、count = 2
        lock.unlock();// owner = mainThread、count = 1
        lock.unlock();// owner = null、count = 0
    }
```

`ReentrantLock`实现思路：

```java
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.LockSupport;

public class MyReentrantLock implements Lock {

    /**
     * 锁的重入次数
     */
    private final AtomicInteger count = new AtomicInteger();
    /**
     * 当前持有锁的线程
     */
    private volatile Thread owner;

    /**
     * 相当于 synchronized 监视器锁中的 waitSet
     */
    private final ConcurrentLinkedQueue<Thread> waiters = new ConcurrentLinkedQueue<>();

    @Override
    public void lock() {
        waiters.offer(Thread.currentThread());
        for (; ; ) {
            Thread thread = waiters.peek();
            if (thread != Thread.currentThread()) {
                LockSupport.park();
                continue;
            }
            boolean succ = tryLock();
            if (!succ) {
                LockSupport.park();
                continue;
            }
            waiters.poll();
            return;
        }
    }

    @Override
    public void lockInterruptibly() throws InterruptedException {

    }

    @Override
    public boolean tryLock() {
        int ct = count.get();
        if (ct == 0) {
            // 锁没被占用,抢锁
            boolean success = count.compareAndSet(ct, ct + 1);
            if (success) {
                // 抢到了
                owner = Thread.currentThread();
            }
            return success;
        } else {
            // 锁被占用
            if (owner == Thread.currentThread()) {
                // 锁被当前线程占用，重入次数++
                count.set(ct + 1);
                return true;
            } else {
                // 锁被其他线程占用
                return false;
            }
        }
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return false;
    }

    @Override
    public void unlock() {
        if (!tryUnlock()) {
            return;
        }
        Thread thread = waiters.peek();
        if (thread == null) {
            return;
        }
        LockSupport.unpark(thread);
    }

    /**
     * @return 重入次数彻底归零，释放掉锁
     */
    private boolean tryUnlock() {
        if (owner != Thread.currentThread()) {
            throw new IllegalMonitorStateException();
        }
        int ct = count.get();
        int newCt = ct - 1;
        count.set(newCt);
        if (newCt == 0) {
            owner = null;
            return true;
        }
        return false;
    }

    @Override
    public Condition newCondition() {
        return null;
    }
}
```

##### synchronized vs Lock

- synchronized
  - 优点
    - 使用简单，语义清晰，哪里需要点哪里。
    - 由JVM提供，提供了多种优化方案（锁粗化、锁消除、偏向锁、轻量级锁）
    - 锁的释放由虚拟机来完成，不用人工干预，也降低了死锁的可能性
  - 缺点
    - 无法实现一些锁的高级功能如：公平锁、中断锁、超时锁、读写锁、共享锁等
- Lock
  - 优点
    - 所有synchronized的缺点
    - 可以实现更多的功能，让synchronized缺点更多
  - 缺点
    - 需手动释放锁unlock，新手使用不当可能造成死锁
- synchronized是卡片机，Lock是单反 
