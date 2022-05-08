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

55:54
