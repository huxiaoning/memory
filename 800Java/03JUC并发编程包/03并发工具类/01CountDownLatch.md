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

07:51

