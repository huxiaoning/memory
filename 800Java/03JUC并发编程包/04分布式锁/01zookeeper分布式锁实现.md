# zookeeper分布式锁实现

- 引入依赖

  - ```xml
            <dependency>
                <groupId>com.101tec</groupId>
                <artifactId>zkclient</artifactId>
                <version>0.11</version>
            </dependency>
    ```

- 实现一

  - ```java
    package org.example;
    
    import org.I0Itec.zkclient.IZkDataListener;
    import org.I0Itec.zkclient.ZkClient;
    import org.I0Itec.zkclient.exception.ZkMarshallingError;
    import org.I0Itec.zkclient.serialize.ZkSerializer;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.locks.Condition;
    import java.util.concurrent.locks.Lock;
    
    /**
     * 基于zookeeper的分布式锁
     *
     * @author Jett
     */
    public class ZookeeperDistributeLock implements Lock {
    
        private final String lockPath;
    
        private final ZkClient zkClient;
    
        public ZookeeperDistributeLock(String lockPath) {
            this.lockPath = lockPath;
            this.zkClient = new ZkClient("localhost:2181");
            this.zkClient.setZkSerializer(new ZkSerializer() {
                @Override
                public byte[] serialize(Object data) throws ZkMarshallingError {
                    return new byte[0];
                }
    
                @Override
                public Object deserialize(byte[] bytes) throws ZkMarshallingError {
                    return null;
                }
            });
        }
    
        @Override
        public void lock() {
            for (; ; ) {
                boolean success = tryLock();
                if (success) {
                    break;
                }
                waitForLock();
            }
        }
    
        private void waitForLock() {
            CountDownLatch cdl = new CountDownLatch(1);
            IZkDataListener listener = new IZkDataListener() {
                @Override
                public void handleDataChange(String dataPath, Object data) throws Exception {
                    cdl.countDown();
                }
    
                @Override
                public void handleDataDeleted(String dataPath) throws Exception {
                }
            };
            // 每个线程都注册监听，产生惊群效应(理论上每个进程订阅一个即可)
            this.zkClient.subscribeDataChanges(this.lockPath, listener);
            if (this.zkClient.exists(this.lockPath)) {
                try {
                    cdl.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            this.zkClient.unsubscribeDataChanges(this.lockPath, listener);
        }
        @Override
        public boolean tryLock() {
            try {
                zkClient.createEphemeral(lockPath);
                return true;
            } catch (RuntimeException e) {
                return false;
            }
        }
        @Override
        public void unlock() {
            // 有问题，应该看一下当前锁持有者是否是自已
            zkClient.delete(lockPath);
        }
        // 省略其他方法...
    }
    ```

- 实现二：利用zk特性-临时顺序节点，解决上面的惊群效应

  - ```java
        @Override
        public boolean tryLock() {
            if (this.currentPath == null) {
                // 这句代码不确定
                this.currentPath = this.zkClient.createEphemeralSequential(this.lockPath, null);
            }
            List<String> childrenList = this.zkClient.getChildren(this.lockPath);
            Collections.sort(childrenList);
            if (this.currentPath.equals(lockPath + "/" + childrenList.get(0))) {
                return true;
            }
            // 这句代码不确定
            int currIdx = childrenList.indexOf(this.currentPath.substring(lockPath.length()));
            this.beforePath = lockPath + "/" + childrenList.get(currIdx - 1);
            return false;
        }
    ```



##### 有现成的实现方案

```
https://www.runoob.com/w3cnote/zookeeper-locks.html
```

