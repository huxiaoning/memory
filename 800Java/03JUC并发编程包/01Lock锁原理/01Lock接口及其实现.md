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

