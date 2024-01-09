# 通过Future来实现取消

**Future拥有一个cancel方法，该方法带有一个boolean类型的参数mayInterruptIfRunning：**

* 如果**mayInterruptIfRunning为true**并且任务当前**正在某个线程中运行**，那么**这个线程将被中断**。
* 如果**这个参数为false**，那么意味着“若任务还没有启动，就不要运行它”，**这种方式应该用于那些不处理中断的任务中。**

<mark style="color:orange;">**除非你清楚线程的中断策略，否则不要中断线程**</mark>，那么在什么情况下调用cancel可以将参数指定为true？**执行任务的线程是由标准的Executor创建的**，它实现了一种中断策略使得任务可以通过中断被取消，所以**如果任务在标准Executor中运行，并通过它们的Future来取消任务，那么可以将mayInterruptIfRunning设置为true。**

```java
private static ExecutorService taskExec = Executors.newCachedThreadPool();

public static void timeRun(Runnable r, long timeout, TimeUnit unit) 
        throws InterruptedException {
    Future<?> task = taskExec.submit(r);
    try {
        task.get(timeout, unit);
    } catch (ExecutionException e) {
        throw new RuntimeException(e);
    } catch (TimeoutException e) {
        e.printStackTrace();
    } finally {
        task.cancel(true);
    }
}
```
