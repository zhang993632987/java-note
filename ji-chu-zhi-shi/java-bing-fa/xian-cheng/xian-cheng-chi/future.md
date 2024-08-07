# Future

**Future 表示一个任务的生命周期**，并提供了相应的方法来**判断是否已经完成或取消**，以及**获取任务的结果**和**取消任务**等。<mark style="color:blue;">**在 Future 规范中包含的隐含意义是，任务的生命周期只能前进，不能后退。当某个任务完成后，它就永远停留在“完成”状态上。**</mark>

<details>

<summary><mark style="color:purple;">Callable 接口</mark></summary>

```java
@FunctionalInterface
public interface Callable<V> {
    
    V call() throws Exception;
}
```

</details>

<details>

<summary><mark style="color:purple;">Future 接口</mark></summary>

```java
public interface Future<V> {

    /**
     * 如果任务还未开始执行，取消后该任务将不会运
     * 如果任务已经开始执行，参数 mayInterruptIfRunning 将决定
     *          是否尝试中断线程来停止任务执行
     */
    boolean cancel(boolean mayInterruptIfRunning);

    boolean isCancelled();

    boolean isDone();

    V get() throws InterruptedException, ExecutionException;

    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

</details>

* get 方法的行为取决于任务的状态（**尚未开始**、**正在运行**、**已完成**）：
  * **如果任务已经完成，那么 get 会立即返回或者抛出一个 Exception**，**如果任务没有完成，那么 get 将阻塞并直到任务完成。**
  * 如果任务抛出了异常，那么 get 将该异常封装为 **ExecutionException** 并重新抛出。如果 get 抛出了 ExecutionException，那么可以通过 **getCause** 来获得被封装的初始异常。
  * 如果任务被取消，那么 get 将抛出 **CancellationException**。

## 创建 Future

可以通过许多种方法创建一个 Future 来描述任务：

*   [x] <mark style="color:blue;">**ExecutorService 中的所有 submit 方法**</mark>都将**返回一个 Future**，从而将一个 Runnable 或 Callable 提交给 Executor，并得到一个 Future 用来获得任务的执行结果或者取消任务。

    > **在将 Runnable 或 Callable 提交到 Executor 的过程中，包含了一个安全发布过程**，**即将 Runnable 或 Callable 从提交线程发布到最终执行任务的线程。**
    >
    > 类似地，**在设置 Future 结果的过程中也包含了一个安全发布，即将这个结果从计算它的线程发布到任何通过 get 获得它的线程。**
*   [x] <mark style="color:blue;">**显式地为某个指定的 Runnable 或 Callable 实例化一个 FutureTask**</mark>。

    <div align="left">

    <figure><img src="../../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

    </div>

{% hint style="success" %}
从 Java 6 开始，ExecutorService 实现可以改写 AbstractExecutorService 中的 newTaskFor 方法，从而根据已提交的 Runnable 或 Callable 来控制 Future 的实例化过程。

在默认实现中仅创建了一个新的 FutureTask：

```java
protected <T> RunnableFuture<T> newTaskFor(Runnable runnable, T value) {
    return new FutureTask<T>(runnable, value);
}
```
{% endhint %}
