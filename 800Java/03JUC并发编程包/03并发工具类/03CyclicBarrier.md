# CyclicBarrier循环障碍

循环栅栏，可以循环利用的屏障。

举例：支麻将馆打麻将时，每到齐四个人，就可以凑一桌。

```java
    public static void main(String[] args) {
        final CyclicBarrier cyclicBarrier = new CyclicBarrier(4, new Runnable() {
            @Override
            public void run() {
                System.out.println("凑齐一桌");
            }
        });
        for (int i = 0; i < 100; i++) {
            int finalI = i;
            new Thread(() -> {
                try {
                    cyclicBarrier.await();
                    System.out.println(finalI + "号上桌打麻将...");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
            LockSupport.parkNanos(1000 * 1000 * 1000L);
        }
    }

凑齐一桌
3号上桌打麻将...
2号上桌打麻将...
0号上桌打麻将...
1号上桌打麻将...
凑齐一桌
7号上桌打麻将...
4号上桌打麻将...
6号上桌打麻将...
5号上桌打麻将...
```



### 用Lock和Condition实现CyclicBarrier

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.LockSupport;
import java.util.concurrent.locks.ReentrantLock;

public class MyCyclicBarrier {
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    // 记录当前这个批次有多少个
    private int count = 0;
    // 记录批次的大小
    private final int parties;
    private final Runnable task;
    // 分代
    private Object generation = new Object();

    public MyCyclicBarrier(int parties, Runnable task) {
        if (parties <= 0) {
            throw new IllegalArgumentException();
        }
        this.parties = parties;
        this.task = task;
    }

    private void nextGeneration() {
        condition.signalAll();
        count = 0;
        generation = new Object();
        if (task != null) {
            task.run();
        }
    }

    public void await() {
        lock.lock();
        try {
            final Object g = generation;
            count++;
            int index = count;
            if (index == parties) {
                nextGeneration();
                return;
            }
            for (; ; ) {
                try {
                    condition.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if (g != generation) {
                    return;
                }
            }
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        final MyCyclicBarrier cyclicBarrier = new MyCyclicBarrier(4, new Runnable() {
            @Override
            public void run() {
                System.out.println("凑齐一桌");
            }
        });
        for (int i = 0; i < 100; i++) {
            int finalI = i;
            new Thread(() -> {
                cyclicBarrier.await();
                System.out.println(finalI + "号上桌打麻将...");
            }).start();
            LockSupport.parkNanos(1000 * 1000 * 1000L);
        }
    }
}
```



