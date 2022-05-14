# `CountDownLatch` 倒计数闩锁



```java
    public static void main(String[] args) throws InterruptedException {
        AtomicLong num = new AtomicLong();
        CountDownLatch countDownLatch = new CountDownLatch(10);
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int j = 0; j < 10000000; j++) {
                        num.getAndIncrement();
                    }
                    countDownLatch.countDown(); // 计数--
                }
            }).start();
        }

        countDownLatch.await(); // 阻塞等待10次 countDown()
        System.out.println(num.get());
    }
```





```java
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(3);
        for (int i = 0; i < 3; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    System.out.println("before ...");
                    countDownLatch.countDown();
                    try {
                        countDownLatch.await();
                        System.out.println("after ...");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }).start();
        }
    }

before ...
before ...
before ...
after ...
after ...
after ...
```



### 用AQS手写CountDownLatch

```java
import java.util.concurrent.locks.AbstractQueuedSynchronizer;

public class MyCountDownLatch {

    private Sync sync;

    public MyCountDownLatch(int count) {
        this.sync = new Sync(count);
    }

    public void countDown() {
        sync.releaseShared(1);
    }

    public void await() {
        sync.acquireShared(1);
    }

    class Sync extends AbstractQueuedSynchronizer {
        public Sync(int count) {
            setState(count);
        }

        @Override
        protected int tryAcquireShared(int arg) {
            return getState() == 0 ? 1 : -1;
        }

        @Override
        protected boolean tryReleaseShared(int arg) {
            for (; ; ) {
                int c = getState();
                if (c == 0) {
                    return false;
                }
                int nextC = c - arg;
                if (compareAndSetState(c, nextC)) {
                    return nextC == 0;
                }
            }
        }
    }

    public static void main(String[] args) {
        int count = 5;
        MyCountDownLatch myCountDownLatch = new MyCountDownLatch(5);
        for (int i = 0; i < count; i++) {
            int finalI = i;
            new Thread(new Runnable() {
                @Override
                public void run() {
                    myCountDownLatch.countDown();
                    System.out.println(finalI);
                }
            }).start();
        }
        myCountDownLatch.await();
        System.out.println("x");
    }
}
```

25:51

