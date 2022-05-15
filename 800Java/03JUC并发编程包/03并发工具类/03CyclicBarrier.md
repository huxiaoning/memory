# CyclicBarrier

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
            new Thread(() -> {
                try {
                    cyclicBarrier.await();
                    System.out.println("开一桌麻将...");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
            LockSupport.parkNanos(1000 * 1000 * 1000L);
        }
    }
```

44:16

