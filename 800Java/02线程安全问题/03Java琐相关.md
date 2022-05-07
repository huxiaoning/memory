# Java琐相关



### `Java`中琐的概念

##### 自旋锁

是指当一个线程在获取锁的时候，如果锁已经被其它线程获取，那么该线程将循环等待，然后不断的判断锁是否能够被成功获取，直到获取到锁才会退出循环。



##### 乐观锁

假定没有冲突，在修改数据时如果发现数据和之前获取的不一致，则读最新数据，修改后重试修改

##### 悲观锁

假定会发生并发冲突，同步所有对数据的相关操作，从读数据就开始上锁。



##### 独享锁（写）

给资源加上写锁，线程可以修改资源，其他线程不能再加锁；（单写）

##### 共享锁（读）

给资源加上读锁后只能读不能改，其他线程也只能加读锁，不能加写锁；（多读）



##### 可重入锁、不可重入锁

线程拿到一把锁之后，可以自由进入同一把锁所同步的其他代码。



##### 公平锁、非公平锁：

争抢锁的顺序，如果是按先来后到，则为公平。



##### 几种重要的锁实现方式

- `synchronized`
- `ReentrantLock`
- `ReentrantReadWriteLock`



### `synchronized`

- 特性

  - 可重入
  - 独享
  - 悲观

- 锁优化

1. 销消除（开启锁消除的参数：`-XX:+DoEscapeAnalysis -XX:+EliminateLocks`） 

在单个线程中使用同步容器或`StringBuffer`这样的线程安全的类时，消除加琐解琐的开销

```java
        StringBuffer stringBuffer = new StringBuffer();
        stringBuffer.append("a");
        stringBuffer.append("b");
        stringBuffer.append("c");
        stringBuffer.append("d");
        stringBuffer.append("e");
```

```java
    @Override
    public synchronized StringBuffer append(String str) {
        toStringCache = null;
        super.append(str);
        return this;
    }
```

开启销消除后，`StringBuffer`类的`append`方法被频繁调用，`JIT`即时编译器会认为append方法的`synchronized`是不存在的。

2. 锁粗化

`JDK`做了锁粗化的优化，但我们自已可从代码层面优化

```java
    public void test() {
        int i = 0;
        synchronized (this) {
            i++;
        }
        synchronized (this) {
            i--;
        }
        System.out.println("123");
        synchronized (this) {
            System.out.println("abc");
        }
        synchronized (this) {
            i++;
        }
        
        // 锁粗化
        synchronized (this) {
            i++;
            i--;
            System.out.println("123");
            System.out.println("abc");
            i++;
        }
    }
```



Note：`synchronized`关键字，不仅实现同步，
`JMM`中规定，`synchronized`要保证可见性（不能够被缓存）。



### `synchronized`是如何记录加锁的状态标识的？

状态会被记录到锁对象中吗？

若锁被其他线程占用，当前线程需要挂起。释放锁时唤醒其他挂起的线程，这些是如何做到的？

##### 堆内存中的Java对象包含对象头信息：

- `Mark Word` 
- `Class Meta address` 指向方法区中的类信息，说明当前的Java对象是哪个类的对象。
- `Array Length` 如果当前对象是数组，对象头中会记录数组的长度。

##### Mark Word

| Bitfields               |      |      | Tag  | State               | 说明               |
| ----------------------- | ---- | ---- | ---- | ------------------- | ------------------ |
| Hashcode                | Age  | 0    | 01   | Unlocked            | 未锁定             |
| Thread ID               | Age  | 1    | 01   | Biased/biasable     | 偏向锁             |
| Lock record address     |      |      | 00   | Light-weight locked | 轻量级锁           |
| Monitor address         |      |      | 10   | Heavy-weight locked | 重量级锁(监视器锁) |
| Forwarding address,etc. |      |      | 11   | Marked for GC       | GC标记             |

找到一篇有意思的[文章](https://blog.csdn.net/qq_36434742/article/details/106854061)。

```xml
        <dependency>
            <groupId>org.openjdk.jol</groupId>
            <artifactId>jol-core</artifactId>
            <version>0.16</version>
        </dependency>
```



```java
import org.openjdk.jol.info.ClassLayout;
import org.openjdk.jol.vm.VM;
public class MarkWordDemo {
    private boolean flag;
    public static void main(String[] args) {
        MarkWordDemo markWordDemo = new MarkWordDemo();
        System.out.println(VM.current().details());
        markWordDemo.print();
        markWordDemo.printWithLock();
        for (int i = 0; i < 2; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int j = 0; j < 2; j++) {
                        markWordDemo.printWithLock();
                    }
                }
            }).start();
        }
    }
    public void print() {
        System.out.println(ClassLayout.parseInstance(this).toPrintable());
    }
    public synchronized void printWithLock() {
        System.out.println(ClassLayout.parseInstance(this).toPrintable());
    }
}
```

输出如下:

```shell
# Running 64-bit HotSpot VM.
# Using compressed oop with 3-bit shift.
# Using compressed klass with 3-bit shift.
# Objects are 8 bytes aligned.
# Field sizes by type: 4, 1, 1, 2, 2, 4, 4, 8, 8 [bytes]
# Array element sizes: 4, 1, 1, 2, 2, 4, 4, 8, 8 [bytes]

org.example.header.MarkWordDemo object internals:
OFF  SZ      TYPE DESCRIPTION               VALUE
  0   8           (object header: mark)     0x0000000000000001 (non-biasable; age: 0)
  8   4           (object header: class)    0xf800c105
 12   1   boolean MarkWordDemo.flag         false
 13   3           (object alignment gap)    
Instance size: 16 bytes
Space losses: 0 bytes internal + 3 bytes external = 3 bytes total

org.example.header.MarkWordDemo object internals:
OFF  SZ      TYPE DESCRIPTION               VALUE
  0   8           (object header: mark)     0x000000343afff2f8 (thin lock: 0x000000343afff2f8)
  8   4           (object header: class)    0xf800c105
 12   1   boolean MarkWordDemo.flag         false
 13   3           (object alignment gap)    
Instance size: 16 bytes
Space losses: 0 bytes internal + 3 bytes external = 3 bytes total

org.example.header.MarkWordDemo object internals:
OFF  SZ      TYPE DESCRIPTION               VALUE
  0   8           (object header: mark)     0x00000230132e501a (fat lock: 0x00000230132e501a)
  8   4           (object header: class)    0xf800c105
 12   1   boolean MarkWordDemo.flag         false
 13   3           (object alignment gap)    
Instance size: 16 bytes
Space losses: 0 bytes internal + 3 bytes external = 3 bytes total
```

##### 加锁原理

cas修改markword。cas(Hashcode行,Lock record address行)，

Lock record address指向抢锁成功的线程。

抢锁失败的线程，会继续自旋，但有一定的次数限制，超过次数就升级锁。此时的markword被置为`Monitor address`。

cas自旋，加的是一个轻量级锁。自旋超过一定次数，就升级为重量级锁。

重量级锁，即监视器锁，其[原理](https://blog.csdn.net/weixin_43364551/article/details/117163224)。

```java
/**
 * 对象监视器示意(其实应该是C++对象结构体)
 * https://blog.csdn.net/weixin_43364551/article/details/117163224
 */
public class ObjectMonitor {
    /**
     * 由于synchronized是可重入锁，count用于记录当前对象锁拥有者线程获取锁的次数
     */
    private int count;
    /**
     * 处于等待锁block状态的线程，会被加入到EntryList
     */
    private List<Thread> entryList;

    /**
     * 调用了wait方法，处于WAIT/TIME_WAIT的线程，会被加入到WaitSet
     */
    private Set<Thread> waitSet;

    /**
     * 获得锁的线程对象
     * waiteSet中的线程，被其他线程notify唤醒后，将会成为锁对象的拥有者
     */
    private Thread owner;
}
```



##### 偏向锁

在JDK6以后，默认已经开启了偏向锁这个优化，通过JVM参数`-XX:-UseBiasedLocking`来禁用偏向锁若偏向锁开启，只有一个线程抢锁，可获取到偏向锁。

| Bitfields |      |      | Tag  | State           | 说明                                     |
| --------- | ---- | ---- | ---- | --------------- | ---------------------------------------- |
| Hashcode  | Age  | 0    | 01   | Unlocked        | 未锁定,关闭偏向锁`-XX:-UseBiasedLocking` |
|           | Age  | 1    | 01   | Biased/biasable | 未锁定,开启偏向锁                        |
| Thread ID | Age  | 1    | 01   | Biased/biasable | 锁定，偏向锁                             |

减少cas消耗。

`synchronized`锁，只能升级，不能降级。

