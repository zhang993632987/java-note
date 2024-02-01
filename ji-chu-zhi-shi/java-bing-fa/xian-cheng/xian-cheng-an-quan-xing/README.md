# 线程安全性

<mark style="color:blue;">**要编写线程安全的代码，其核心在于要对状态访问操作进行管理**</mark>，特别是对**共享的（Shared）**和**可变的（Mutable）**状态的访问。

* “共享”意味着变量可以由多个线程同时访问
* 而“可变”则意味着变量的值在其生命周期内可以发生变化

{% hint style="success" %}
<mark style="color:blue;">**访问某个变量的代码越少，就越容易确保对变量的所有访问都实现正确同步**</mark>，同时也更容易找出变量在哪些条件下被访问。因此，<mark style="color:blue;">**程序状态的封装性越好，就越容易实现程序的线程安全性**</mark>，并且代码的维护人员也越容易保持这种方式。
{% endhint %}

## 什么是线程安全性

<mark style="color:blue;">**当多个线程访问某个类时，不管运行时环境采用何种调度方式或者这些线程将如何交替执行，并且在主调代码中不需要任何额外的同步或协同，这个类都能表现出正确的行为，那么就称这个类是线程安全的。**</mark>

正确性的含义是，某个类的行为与其规范完全一致。在良好的规范中通常会定义各种**不变性条件（Invariant）**来**约束对象的状态**，以及定义各种**后验条件（Postcondition）**来**描述对象操作的结果**。

{% hint style="info" %}
## <mark style="color:blue;">提示</mark>

1. **无状态对象一定是线程安全的。**
2. **当在无状态的类中添加一个状态时，如果该状态完全由线程安全的对象来管理，那么这个类仍然是线程安全的。**

然而，当状态变量的数量由一个变为多个时，并不会像状态变量数量由零个变为一个那样简单。
{% endhint %}

## 竞态条件

最常见的竞态条件类型就是<mark style="color:blue;">**“先检查后执行（Check-Then-Act）”**</mark>操作，即**通过一个可能失效的观测结果来决定下一步的动作。**

* 首先观察到某个条件为真（例如文件 X 不存在），然后根据这个观察结果采用相应的动作（创建文件X）
* 但事实上，在你观察到这个结果以及开始创建文件之间，观察结果可能变得无效（另一个线程在这期间创建了文件 X），从而导致各种问题（未预期的异常、数据被覆盖、文件被破坏等）。

{% hint style="success" %}
<mark style="color:blue;">**要避免竞态条件问题，就必须在某个线程修改该变量时，通过某种方式防止其他线程使用这个变量**</mark>，从而确保其他线程只能在**修改操作完成之前或之后**读取和修改状态，而不是在修改状态的过程中。
{% endhint %}

### 示例：延迟初始化中的竞态条件

延迟初始化的目的是将对象的初始化操作推迟到实际被使用时才进行，同时要确保只被初始化一次。

```java
public class LazyInitRace {
    private ExpensiveObject instance = null;
    public ExpensiveObject getInstance() {
        if (instance == null)
            instance = new ExpensiveObject();
        return instance;
    }
}
```

## 用锁来保护状态

{% hint style="warning" %}
## <mark style="color:orange;">注意</mark>

* **如果用同步来协调对某个变量的访问，那么在**<mark style="color:orange;">**访问这个变量的所有位置上都需要使用同步**</mark>。
* **当使用锁来协调对某个变量的访问时，在**<mark style="color:orange;">**访问变量的所有位置上都要使用同一个锁**</mark>**。**
* **对于每个包含多个变量的不变性条件，其中**<mark style="color:orange;">**涉及的所有变量都需要由同一个锁来保护**</mark>**。**
{% endhint %}

<details>

<summary><mark style="color:purple;">示例：因式分解</mark></summary>

```java
public class CachedFactorizer implements Servlet {
    private BigInteger lastNumber;
    private BigInteger[] lastFactors;
    private long hits;
    private long cacheHits;
    
    public synchronized long getHits() { return hits; }
    public synchronized long getCacheHitRatio {
        return (double) cacheHits / hits;
    }
    
    public void service(ServletRequest request, ServletResponse response) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = null;
        synchronized(this) {
            ++hits;
            if (i.equals(lastNumber)) {
                cacheHits++;
                factors = lastFactors.clone();
            }
        }
        if (factors == null) {
            factors = factor(i);
            synchronized(this) {
                lastNumber = i;
                lasteFactories = factors.clone();
            }
        }
        encodeIntoReponse(response, factors);
    }
}
```

</details>
