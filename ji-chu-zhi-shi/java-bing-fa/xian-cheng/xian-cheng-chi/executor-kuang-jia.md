# Executor 框架

<details>

<summary><mark style="color:purple;">Executor接口</mark></summary>

```java
public interface Executor {

    /**
     * Executes the given command at some time in the future.  The command
     * may execute in a new thread, in a pooled thread, or in the calling
     * thread, at the discretion of the {@code Executor} implementation.
     *
     * @param command the runnable task
     * @throws RejectedExecutionException if this task cannot be
     * accepted for execution
     * @throws NullPointerException if command is null
     */
    void execute(Runnable command);
}
```

</details>

* Executor 提供了一种标准的方法**将任务的提交过程与执行过程解耦开来**，并**用 Runnable 来表示任务**。
* **Executor 基于生产者-消费者模式**
  * 提交任务的操作相当于生产者（生成待完成的工作单元）
  * 执行任务的线程则相当于消费者（执行完这些工作单元）
* Executor 执行的任务有 4 个生命周期阶段：**创建**、**提交**、**开始**和**完成**。
  * **已提交但尚未开始的任务可以取消。**
  * 对于那些**已经开始执行的任务**，**只有当它们能响应中断时，才能取消**。
  * 取消一个**已经完成的任务**不会有任何影响。

{% hint style="success" %}
Runnable 是一种有很大局限的抽象，虽然 run 能写入到日志文件或者将结果放入某个共享的数据结构，但它不能返回一个值或抛出一个受检查的异常。
{% endhint %}
