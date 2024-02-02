# 通过Future来实现取消

**Future 拥有一个 cancel 方法，该方法带有一个 boolean 类型的参数 mayInterruptIfRunning：**

* 如果 **mayInterruptIfRunning 为 true** 并且任务当前**正在某个线程中运行**，那么**这个线程将被中断**。
* 如果**这个参数为 false**，那么意味着“若任务还没有启动，就不要运行它”**。**

{% hint style="info" %}
## <mark style="color:orange;">注意</mark>

**除非你清楚线程的中断策略，否则不要中断线程。**
{% endhint %}

{% hint style="success" %}
**在什么情况下调用 cancel 可以将参数指定为 true？**

**如果执行任务的线程是由标准的 Executor 创建的**，而它实现了一种中断策略使得任务可以通过中断被取消，所以**如果任务在标准 Executor 中运行，并通过它们的 Future 来取消任务，那么可以将 mayInterruptIfRunning 设置为 true。**
{% endhint %}

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
