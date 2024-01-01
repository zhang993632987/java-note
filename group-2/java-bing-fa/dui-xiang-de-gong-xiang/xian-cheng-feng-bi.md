# 线程封闭

如果仅在单线程内访问数据，就不需要同步，这种技术被称为**线程封闭（Thread Confinement）**。

## 栈封闭

在栈封闭中，只能通过局部变量才能访问对象。

## ThreadLocal 类

ThreadLocal 类的使用方式如下：

```java
private static ThreadLocal<Connection> connectionHolder = new ThreadLocal<>() {
    public Connection initialValue() {
        return DriverManager.getConnection(DB_URL);  
    }
};

public static Connection getConnection() {
    return connectionHolder.get();
}
```

当某个线程第一次调用 ThreadLocal.get 方法时，就会调用 initialValue 来获取初始值。

> 从概念上看，可以将 ThreadLocal\<T> 视为包含了 Map\<Thread, T> 对象，其中保存了特定于该线程的值。但 ThreadLocal 的实现并非如此，这些特定于线程的值保存在 Thread 对象中，当线程终止后，这些值会作为垃圾回收。
>
>
