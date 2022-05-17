# ForkJoin框架



ForkJoinPool是ExecutorService接口的实现，它专为可以递归分解成小块的工作而设计。

fork／join框架将任务分配给线程池中的工作线程，充分利用多处理器的优势，提高程序性能。

使用fork／join框架的第一步是编写执行一部分工作的代码。类似的伪代码如下：

```
如果（当前工作部分足够小）
	直接做这项工作 
其他
	把当前工作分成两部分
	调用这两个部分并等待结果
```

将此代码包装在ForkJoinTask子类中，通常是RecursiveTask（可以返回结果）或RecursiveAction。 

```java
package org.example.fork;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;
import java.util.concurrent.RecursiveTask;

public class Job extends RecursiveTask<List<String>> {

    private List<String> urls;
    private int start;
    private int end;

    public Job(List<String> urls, int start, int end) {
        this.urls = urls;
        this.start = start;
        this.end = end;
    }

    public String doRequest(String url) {
        // 模拟网络请求
        return "Kody ... test ... " + url;
    }

    @Override
    protected List<String> compute() {
        int count = end - start;
        if (count <= 10) {
            // 直接执行
            List<String> list = new ArrayList<>();
            for (int i = start; i < end; i++) {
                list.add(doRequest(urls.get(i)));
            }
            return list;
        } else {
            // 继续拆分任务
            int x = (start + end) / 2;
            Job job1 = new Job(urls, start, x);
            job1.fork();

            Job job2 = new Job(urls, x, end);
            job2.fork();

            List<String> list = new ArrayList<>();
            list.addAll(job1.join());
            list.addAll(job2.join());
            return list;
        }
    }

    public static void main(String[] args) {
        List<String> urls = new ArrayList<>();
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        urls.add("baidu.com");
        urls.add("sina.com");
        
        // ExecutorService executorService = Executors.newWorkStealingPool();
        ForkJoinPool forkJoinPool = new ForkJoinPool
                (Runtime.getRuntime().availableProcessors(),
                        ForkJoinPool.defaultForkJoinWorkerThreadFactory,
                        null, true);

        Job job = new Job(urls, 0, urls.size());
        ForkJoinTask<List<String>> forkJoinTask = forkJoinPool.submit(job);
        try {
            List<String> retList = forkJoinTask.get();
            for (String each : retList) {
                System.out.println(each);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

