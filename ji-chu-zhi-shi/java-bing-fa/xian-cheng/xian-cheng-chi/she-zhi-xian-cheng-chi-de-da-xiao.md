# 设置线程池的大小

**线程池的理想大小取决于被提交任务的类型以及所部署系统的特性。**

{% hint style="success" %}
要设置线程池的大小并不困难，只需要<mark style="color:orange;">**避免“过大”和“过小”这两种极端情况**</mark>。

* **如果线程池过大，那么大量的线程将在相对很少的 CPU 和内存资源上发生竞争，这不仅会导致更高的内存使用量，而且还可能耗尽资源。**
* **如果线程池过小，那么将导致许多空闲的处理器无法执行工作，从而降低吞吐率。**
{% endhint %}

{% hint style="warning" %}
如果需要**执行不同类别的任务**，并且它们之间的**行为相差很大**，那么应该考虑<mark style="color:orange;">**使用多个线程池**</mark>，从而<mark style="color:orange;">**使每个线程池可以根据各自的工作负载来调整**</mark>。
{% endhint %}

## CPU 利用率

对于<mark style="color:blue;">**计算密集型**</mark>的任务，**在拥有 N 个处理器的系统上，当线程池的大小为 N + 1 时，通常能实现最优的利用率**。（即使**当计算密集型的线程偶尔由于页缺失故障或者其他原因而暂停时**，**这个“额外”的线程**也**能确保CPU的时钟周期不会被浪费**。）

对于<mark style="color:blue;">**包含 I/O 操作或者其他阻塞操作**</mark>的任务，由于线程并不会一直执行，因此线程池的规模应该更大。要正确地设置线程池的大小，<mark style="color:blue;">**必须估算出任务的等待时间与计算时间的比值**</mark>。这种估算不需要很精确，并且可以通过一些分析或监控工具来获得。

给定下列定义：

$$
N = \text{CPU数量} \\ U = \text{目标CPU利用率}，0 \le U \le 1 \\ \frac{W}{C} = \text{等待时间与计算时间的比值}
$$

要使处理器达到期望的使用率，线程池的最优大小等于：

$$
\text{线程数量} = N \times U \div \frac{C}{W + C} = N \times U \times (1 + \frac{W}{C})
$$

{% hint style="success" %}
可以通过 Runtime 来获得系统中 CPU 的数目：

```java
int N = Runtime.getRuntime().availableProcessors();
```
{% endhint %}

## 其他因素

CPU 周期并不是唯一影响线程池大小的资源，还包括**内存**、**文件句柄**、**套接字句柄**和**数据库连接**等。计算这些资源对线程池的约束条件是更容易的：<mark style="color:blue;">**计算每个任务对该资源的需求量，然后用该资源的可用总量除以每个任务的需求量，所得结果就是线程池大小的上限。**</mark>

<mark style="color:blue;">**当任务需要某种通过资源池来管理的资源时，例如数据库连接，那么线程池和资源池的大小将会相互影响。**</mark>

* 如果每个任务都需要一个数据库连接，那么连接池的大小就限制了线程池的大小。
* 同样，当线程池中的任务是数据库连接的唯一使用者时，那么线程池的大小又将限制连接池的大小。
