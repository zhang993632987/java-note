# 自定义的取消标志

**设置一个“已请求取消（Cancellation Requested）”标志，而任务将定期地查看该标志。**如果设置了这个标志，那么任务将提前结束。

## 示例

下面程序清单中使用了这项技术，其中的 **PrimeGenerator** 持续地枚举素数，直到它被取消。**cancel 方法将设置 cancelled 标志，并且主循环在搜索下一个素数之前会首先检查这个标志**。

> <mark style="color:orange;">**为了使这个过程能可靠地工作，标志 cancelled 必须为 volatile 类型。**</mark>

```java
public class PrimeGenerator {
    
    private volatile boolean cancelled;
    
    private final List<BigInteger> primes = new ArrayList<>();
    
    public void run() {
        BigInteger p = BigInteger.ONE;
        while (!cancelled) {
            p = p.nextProbablePrime();
            synchronized (this) {
                primes.add(p);
            }
        }
    }
    
    public void cancel() {
        cancelled = true;
    }
    
    public synchronized List<BigInteger> get() {
        return new ArrayList<>(primes);
    }
}
```

PrimeGenerator 使用了一种简单的取消策略：**客户代码通过调用 cancel 来请求取消，PrimeGenerator 在每次搜索素数前首先检查是否存在取消请求，如果存在则退出。**
