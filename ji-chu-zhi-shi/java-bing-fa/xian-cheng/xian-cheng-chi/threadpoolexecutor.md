# ThreadPoolExecutor

<details>

<summary><mark style="color:purple;">ThreadPoolExecutor 的通用构造函数</mark></summary>

```java
public ThreadPoolExecutor(int corePoolSize,     
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

</details>

## 线程的创建与销毁

线程池的<mark style="color:blue;">**基本大小（Core Pool Size）**</mark>、<mark style="color:blue;">**最大大小（Maximum Pool Size）**</mark>以及<mark style="color:blue;">**存活时间**</mark>等因素共同负责线程的创建与销毁。

* **基本大小**也就是线程池的目标大小，即**在没有任务执行时线程池的大小**，并且<mark style="color:orange;">**只有在工作队列满了的情况下才会创建超出这个数量的线程**</mark>。
* 线程池的**最大大小**表示**可同时活动的线程数量的上限**。
* 如果某个线程的空闲时间超过了存活时间，那么将被标记为可回收的，并且当线程池的当前大小超过了基本大小时，这个线程将被终止。

{% hint style="info" %}
<mark style="color:blue;">**在创建 ThreadPoolExecutor 初期，线程并不会立即启动，而是等到有任务提交时才会启动，除非调用 prestartAllCoreThreads。**</mark>
{% endhint %}

{% hint style="success" %}
**如果将线程池的基本大小设置为零，但没有使用 SynchronousQueue 作为其工作队列**，这种方式将产生一些奇怪的行为。

<mark style="color:orange;">**当线程池中的线程数量等于线程池的基本大小时，仅当在工作队列已满的情况下ThreadPoolExecutor 才会创建新的线程。**</mark>因此，**如果线程池的基本大小为零并且其工作队列有一定的容量**，那么当把任务提交给该线程池时，**只有当线程池的工作队列被填满后，才会开始执行任务。**

**在 Java 6 中，可以通过 allowCoreThreadTimeOut 来使线程池中的所有线程超时。**
{% endhint %}

## 管理队列任务

ThreadPoolExecutor 允许提供一个 BlockingQueue 来保存等待执行的任务。基本的任务排队方法有 3 种：**无界队列**、**有界队列**和**同步移交**。

一种稳妥的资源管理策略是<mark style="color:orange;">**使用有界队列**</mark>，例如 **ArrayBlockingQueue**、**有界的 LinkedBlockingQueue**、**PriorityBlockingQueue**。<mark style="color:blue;">**有界队列有助于避免资源耗尽的情况发生。**</mark>

**对于非常大的或者无界的线程池，可以通过使用 SynchronousQueue 来避免任务排队，直接将任务从生产者移交给工作者线程。**

{% hint style="success" %}
SynchronousQueue 不是一个真正的队列，而是一种在线程之间进行移交的机制。**要将一个元素放入 SynchronousQueue 中，必须有另一个线程正在等待接受这个元素。**如果没有线程正在等待，并且线程池的当前大小小于最大值，那么 ThreadPoolExecutor 将创建一个新的线程，否则根据饱和策略，这个任务将被拒绝。

使用直接移交将更高效，因为任务会直接移交给执行它的线程，而不是被首先放在队列中，然后由工作者线程从队列中提取该任务。

<mark style="color:orange;">**只有当线程池是无界的或者可以拒绝任务时，SynchronousQueue 才有实际价值。**</mark>
{% endhint %}

{% hint style="success" %}
<mark style="color:orange;">**只有当任务相互独立时，为线程池或工作队列设置界限才是合理的。**</mark>

如果任务之间存在依赖性，那么有界的线程池或队列就可能导致线程“饥饿”死锁问题。此时应该使用无界的线程池，例如 newCachedThreadPool。

对于提交其他任务并等待其结果的任务来说，还有另一种配置方法，就是使用有界的线程池，并使用 SynchronousQueue 作为工作队列，以及“**调用者运行（Caller-Runs）**”饱和策略。当线程池资源不足时，将变成顺序执行逻辑。
{% endhint %}

## 饱和策略

当有界队列被填满后，饱和策略开始发挥作用。（如果某个任务被提交到一个已被关闭的 Executor 时，也会用到饱和策略。）ThreadPoolExecutor 的饱和策略可以通过调用 setRejectedExecutionHandler 来修改。

JDK 提供了几种不同的饱和策略：**AbortPolicy**、**CallerRunsPolicy**、**DiscardPolicy** 和**DiscardOldestPolicy：**

* “**中止（Abort）**”策略是**默认的饱和策略**，**该策略将抛出未检查的 RejectedExecutionException**。
* 当新提交的任务无法保存到队列中等待执行时，**“抛弃（Discard）”**策略会悄悄**抛弃该任务**。
* “**抛弃最旧的（Discard-Oldest）**”策略则会**抛弃下一个将被执行的任务**，**然后尝试重新提交新的任务。**（<mark style="color:orange;">**如果工作队列是一个优先队列，那么“抛弃最旧的”策略将导致抛弃优先级最高的任务**</mark>）
* “**调用者运行（Caller-Runs）**”策略既不会抛弃任务，也不会抛出异常，而是**将某些任务回退到调用者**，从而降低新任务的流量。它**不会在线程池的某个线程中执行新提交的任务，而是在调用 execute 方法的线程中执行该任务。**

## 线程工厂

**每当线程池需要创建一个线程时，都是通过线程工厂方法来完成的**。默认的线程工厂方法将创建一个新的、非守护的线程，并且不包含特殊的配置信息。

在 **ThreadFactory** 中只定义了一个方法 **newThread**，每当线程池需要创建一个新线程时都会调用这个方法。

## 扩展 ThreadPoolExecutor

ThreadPoolExecutor 是可扩展的，它提供了几个可以在子类化中改写的方法：**beforeExecute**、**afterExecute** 和 **terminated**，这些方法可以用于扩展 ThreadPoolExecutor 的行为。

* 无论任务是从 run 中正常返回，还是抛出一个异常而返回，afterExecute 都会被调用。（如果任务在完成后带有一个 Error，那么就不会调用 afterExecute。）
* 如果 beforeExecute 抛出一个 RuntimeException，那么任务将不被执行，并且 afterExecute 也不会被调用。
* 在线程池完成关闭操作时调用 terminated，也就是**在所有任务都已经完成并且所有工作者线程也已经关闭后**。terminated 可以用来释放 Executor 在其生命周期里分配的各种资源，此外还可以执行发送通知、记录日志或者收集 finalize 统计信息等操作。
