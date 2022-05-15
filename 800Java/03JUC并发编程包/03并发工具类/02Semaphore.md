# Semaphore信号量

- Semaphore是一个计数信号量，常用于限制可以访问某些资源（物理或逻辑的）线程数目。
- 简单说，是一种用来控制并发量的共享锁。
- 限流。

```java
    public static void main(String[] args) {
        // 公共男厕有6个坑位
        final Semaphore semaphore = new Semaphore(6);
        // 一千个男人来蹲坑
        for (int i = 0; i < 1000; i++) {
            int finalI = i;
            new Thread(new Runnable() {
                @Override
                public void run() {
                    try {
                        semaphore.acquire();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    squat(finalI);
                    semaphore.release();
                }
            }).start();
        }
    }

    /**
     * 蹲坑
     */
    public static void squat(int i) {
        Random random = new Random();
        int x = random.nextInt(3) + 1; // [1,3]
        System.out.println(i + "号线程蹲坑用时" + x + "秒。");
        LockSupport.parkNanos(x * 1000 * 1000 * 1000L);
    }
```



### 用`AQS`手写Semaphore

```java
import java.util.Random;
import java.util.concurrent.locks.AbstractQueuedSynchronizer;
import java.util.concurrent.locks.LockSupport;

public class MySemaphore {

    private Sync sync;

    public MySemaphore(int permits) {
        this.sync = new Sync(permits);
    }


    public void acquire() {
        sync.acquireShared(1);
    }

    public void release() {
        sync.releaseShared(1);
    }


    class Sync extends AbstractQueuedSynchronizer {
        private int permits;

        public Sync(int permits) {
            this.permits = permits;
        }

        @Override
        protected int tryAcquireShared(int arg) {
            int state = getState();
            int nextState = state + arg;
            if (nextState <= permits) {
                if (compareAndSetState(state, nextState)) {
                    return 1;
                }
            }
            return -1;
        }

        @Override
        protected boolean tryReleaseShared(int arg) {
            int state = getState();
            return compareAndSetState(state, state - arg);
        }
    }

    public static void main(String[] args) {
        // 公共男厕有6个坑位
        final MySemaphore semaphore = new MySemaphore(6);
        // 一千个男人来蹲坑
        for (int i = 0; i < 1000; i++) {
            int finalI = i;
            new Thread(new Runnable() {
                @Override
                public void run() {
                    semaphore.acquire();
                    squat(finalI);
                    semaphore.release();
                }
            }).start();
        }
    }

    /**
     * 蹲坑
     */
    public static void squat(int i) {
        Random random = new Random();
        int x = random.nextInt(3) + 1; // [1,3]
        System.out.println(i + "号线程蹲坑用时" + x + "秒。");
        LockSupport.parkNanos(x * 1000 * 1000 * 1000L);
    }
}
```

