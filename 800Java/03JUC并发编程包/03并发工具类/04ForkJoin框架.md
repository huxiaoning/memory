# ForkJoin框架



### 相关接口类

##### Runnable

```java
public interface Runnable {
    void run();
}
```

##### Callable

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

##### Future

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

##### RunnableFuture

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}
```

##### FutureTask

```java
public class FutureTask<V> implements RunnableFuture<V>{
    // ...
}
```



### FutureTask使用示例

```java
    public static void main(String[] args) {
        FutureTask<String> futureTask = new FutureTask<>(new Callable<String>() {
            @Override
            public String call() throws Exception {
                LockSupport.parkNanos(1000 * 1000 * 1000 * 5L);
                System.out.println("done ..."); // 两次启动线程执行，但这行只输出一次
                return "Finish";
            }
        });

        // 第1次执行
        new Thread(futureTask).start();
        try {
            String s = futureTask.get();
            System.out.println(s);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        // 第2次执行
        new Thread(futureTask).start();
        try {
            String s = futureTask.get();
            System.out.println(s);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }

done ...
Finish
Finish
```

同一个FutureTask对象，交给多个线程执行，只会执行一次。



### 手写FutureTask

```java
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicReference;
import java.util.concurrent.locks.LockSupport;

public class MyFutureTask<V> implements Runnable, Future<V> {

    // FutureTask 只能执行一次
    private volatile int state = NEW;

    private static final int NEW = 0;
    private static final int RUNNING = 1;
    private static final int FINISH = 2;

    private final AtomicReference<Thread> runner = new AtomicReference<>(null);

    /**
     * 执行结果
     */
    private V result;

    private final Callable<V> callable;

    // 等待执行结果的等待队列
    private final LinkedBlockingQueue<Thread> waiters = new LinkedBlockingQueue<>(100);

    public MyFutureTask(Callable<V> callable) {
        this.callable = callable;
    }

    @Override
    public void run() {
        if (state != NEW) {
            return;
        }
        if (!runner.compareAndSet(null, Thread.currentThread())) {
            return;
        }
        state = RUNNING;
        try {
            result = callable.call();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            state = FINISH;
        }
        for (; ; ) {
            Thread waiter = waiters.poll();
            if (waiter == null) {
                break;
            }
            LockSupport.unpark(waiter);
        }

    }

    @Override
    public V get() throws InterruptedException, ExecutionException {
        if (state != FINISH) {
            waiters.offer(Thread.currentThread());
        }
        while (state != FINISH) {
            LockSupport.park();
        }
        return result;
    }

    @Override
    public boolean cancel(boolean mayInterruptIfRunning) {
        return false;
    }

    @Override
    public boolean isCancelled() {
        return false;
    }

    @Override
    public boolean isDone() {
        return false;
    }


    @Override
    public V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException {
        return null;
    }


    public static void main(String[] args) {
        MyFutureTask<String> futureTask = new MyFutureTask<>(new Callable<String>() {
            @Override
            public String call() throws Exception {
                LockSupport.parkNanos(1000 * 1000 * 1000 * 5L);
                System.out.println("done ..."); // 两次启动线程执行，但这行只输出一次
                return "Finish";
            }
        });

        // 第1次执行
        new Thread(futureTask).start();
        try {
            String s = futureTask.get();
            System.out.println(s);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        // 第2次执行
        new Thread(futureTask).start();
        try {
            String s = futureTask.get();
            System.out.println(s);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

done ...
Finish
Finish
```

98:50