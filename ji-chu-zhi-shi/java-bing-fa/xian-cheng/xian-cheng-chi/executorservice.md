# ExecutorService

{% hint style="success" %}
<mark style="color:blue;">**JVM 只有在所有（非守护）线程全部终止后才会退出。因此，如果无法正确地关闭 Executor，那么 JVM 将无法结束。**</mark>
{% endhint %}

ExecutorService 扩展了 Executor 接口，添加了一些用于生命周期管理的方法（同时还有一些用于任务提交的便利方法）。

<details>

<summary><mark style="color:purple;">ExecutorService中的生命周期管理方法</mark></summary>

```java
public interface ExecutorService extends Executor {

    void shutdown();

    List<Runnable> shutdownNow();

    boolean isShutdown();

    boolean isTerminated();

    boolean awaitTermination(long timeout, TimeUnit unit)
        throws InterruptedException;
    
    // ...... 其他用于任务提交的方法
}

```

</details>

ExecutorService 的生命周期有 3 种状态：**运行**、**关闭**和**已终止**。

* **ExecutorService 在初始创建时处于**<mark style="color:blue;">**运行状态**</mark>。
* <mark style="color:blue;">**shutdown**</mark> 方法将**执行平缓的**<mark style="color:blue;">**关闭**</mark>**过程**：不再接受新的任务，同时**等待已经提交的任务执行完成**——**包括那些还未开始执行的任务**。
* <mark style="color:blue;">**shutdownNow**</mark> 方法将**执行粗暴的**<mark style="color:blue;">**关闭**</mark>**过程**：它将**尝试取消所有运行中的任务**，并且**不再启动队列中尚未开始执行的任务**。
* 在 ExecutorService 关闭后提交的任务将由“拒绝执行处理器（Rejected Execution Handler）”来处理。
* **等所有任务都完成后，ExecutorService 将转入**<mark style="color:blue;">**终止状态**</mark>。
  * 可以调用 **awaitTermination** 来等待 ExecutorService 到达终止状态，或者通过调用 isTerminated 来轮询 ExecutorService 是否已经终止。
  * 通常在调用 shutdown 之后会立即调用 awaitTermination，从而产生同步地关闭 ExecutorService 的效果。
