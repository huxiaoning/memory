### AQS - AbstractQueuedSynchronizer

### 同步基础方法
##### `getState()`
获取当前同步状态，返回一个整形数值

##### `setState()`

设置状态，接受一个整形数值

##### `compareAndSetState() `

利用`CAS`线程安全的设置状态

##### `setExclusiveOwnerThread(Thread thread)`

设置独占有线程

##### `Thread getExclusiveOwnerThread()`

获取独占线程



### 同步器中可重写的方法

- `tryAccquire() `
- `tryRelease()`
- `tryAccquireShared() `
- `tryReleaseShared() `
- `isHeldExclusively() `

### 同步器中的模版方法 

- `accquire()`
- `accquirelnterruptibly() `
- `tryAccquireNanos() `
- `acquireShared()`
- `accquireSharedlnterruptibly() `
- `tryAccquireSharedNanos() `
- `release()`
- `releaseShared() `
- `getQueuedThreads() `



### 用`AQS`实现自已`ReentrantLock`

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.AbstractQueuedSynchronizer;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;

public class MyReentrantLock implements Lock {

    private final Sync sync = new Sync();

    private class Sync extends AbstractQueuedSynchronizer {

        @Override
        protected boolean tryAcquire(int acquires) {
            int c = getState();
            if (c == 0) {
                boolean success = compareAndSetState(0, acquires);
                if (success) {
                    setExclusiveOwnerThread(Thread.currentThread());
                }
                return success;
            } else {
                if (Thread.currentThread() == getExclusiveOwnerThread()) {
                    int netxc = c + acquires;
                    if (netxc < 0) {
                        throw new Error("");
                    }
                    setState(netxc);
                    return true;
                } else {
                    return false;
                }
            }
        }

        @Override
        protected boolean tryRelease(int releases) {
            if (Thread.currentThread() != getExclusiveOwnerThread()) {
                throw new IllegalMonitorStateException();
            }
            int c = getState() - releases;
            if (c == 0) {
                setExclusiveOwnerThread(null);
                setState(c);
                return true;
            } else {
                setState(c);
                return false;
            }
        }

        public Condition newCondition() {
            return new ConditionObject();
        }

        @Override
        protected boolean isHeldExclusively() {
            return getExclusiveOwnerThread() == Thread.currentThread();
        }
    }

    @Override
    public void lock() {
        sync.acquire(1);
    }

    @Override
    public boolean tryLock() {
        return sync.tryAcquire(1);
    }

    @Override
    public void unlock() {
        sync.release(1);
    }


    @Override
    public void lockInterruptibly() throws InterruptedException {
        sync.acquireInterruptibly(1);
    }

    @Override
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return sync.tryAcquireNanos(1, unit.toNanos(time));
    }

    @Override
    public Condition newCondition() {
        return sync.newCondition();
    }


    public static void main(String[] args) throws InterruptedException {
        final int[] sum = {0};
        final MyReentrantLock myReentrantLock = new MyReentrantLock();
        for (int i = 0; i < 100000; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    myReentrantLock.lock();
                    try {
                        sum[0]++;
                    } finally {
                        myReentrantLock.unlock();
                    }
                }
            }).start();
        }
        Thread.sleep(20000L);
        System.out.println(sum[0]);
    }
}
```

