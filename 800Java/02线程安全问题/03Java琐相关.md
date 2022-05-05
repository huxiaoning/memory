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



40:57
