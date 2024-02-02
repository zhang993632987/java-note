# 线程中断

**线程中断是一种协作机制，一个线程不能强制其他线程停止正在执行的操作而去执行其他的操作。**当线程 A 中断线程 B 时，A 仅仅是要求 B 在执行到某个可以暂停的地方时停止正在执行的操作——前提是线程 B 愿意停止下来。<mark style="color:blue;">**对中断操作的正确理解是：它并不会真正地中断一个正在运行的线程，而只是发出中断请求，然后由线程在下一个合适的时刻中断自己。（这些时刻也被称为取消点）。**</mark>

**每个线程都有一个 boolean 类型的中断状态。当中断线程时，这个线程的中断状态将被设置为 true。**在 Thread 中包含了中断线程以及查询线程中断状态的方法，如下所示：

```java
public class Thread implements Runnable {

    public void interrupt() {}
    
    public boolean isInterrupted() {
        return isInterrupted(false);
    }
    
    public static boolean interrupted() {
        return currentThread().isInterrupted(true);
    }
}
```

* interrupt 方法能中断目标线程
* isInterrupted 方法能返回目标线程的中断状态
* 静态的 interrupted 方法将清除当前线程的中断状态，并返回它之前的值

{% hint style="success" %}
**阻塞库方法，例如 Thread.sleep 和 Object.wait 等，都会检查线程何时中断，并且在发现中断时提前返回。**它们在响应中断时执行的操作包括：清除中断状态，抛出 InterruptedException，表示阻塞操作由于中断而提前结束。
{% endhint %}

## 响应中断

当调用可中断的阻塞函数时，例如 Thread.sleep 或 BlockingQueue.put 等，有两种实用策略可用于处理 InterruptedException：

* **传递 InterruptedException：**传递 InterruptedException 的方法包括，**根本不捕获该异常**，或者**捕获该异常，然后在执行某种简单的清理工作后再次抛出这个异常。**
* **恢复中断：**有时候不能抛出 InterruptedException，例如当代码是 Runnable 的一部分时。在这些情况下，必须**捕获 InterruptedException，并通过调用当前线程上的 interrupt 方法恢复中断状态，这样在调用栈中更高层的代码将看到引发了一个中断。**

{% hint style="danger" %}
## <mark style="color:red;">警告</mark>

<mark style="color:red;">**在出现 InterruptedException 时不应该做的事情是，捕获它但不做出任何响应。**</mark>**这将使调用栈上更高层的代码无法对中断采取处理措施，因为线程被中断的证据已经丢失。**
{% endhint %}
