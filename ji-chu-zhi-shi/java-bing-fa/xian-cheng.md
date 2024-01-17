# 线程

## 线程的状态

<figure><img src="../../.gitbook/assets/ace830df-9919-48ca-91b5-60b193f593d2.png" alt=""><figcaption></figcaption></figure>

Java 中的线程存在 6 种状态：

1.  <mark style="color:blue;">**新建（New）：**</mark>

    Thread 对象被创建，但还未成启动。
2.  <mark style="color:blue;">**可运行（Runnable）：**</mark>

    可能正在运行，也可能正在等待 CPU 时间片。
3.  <mark style="color:blue;">**阻塞（Blocked）：**</mark>

    等待获取一个排他锁。
4.  <mark style="color:blue;">**无限期等待（Waiting）：**</mark>

    等待其它线程显式地唤醒。

    调用 Object.wait()、Thread.join() 或 LockSupport.park()方法后，线程会进入该状态。
5.  <mark style="color:blue;">**限期等待（Timed Waiting）：**</mark>

    调用 Thread.sleep()、Object.wait() 、Thread.join() 、LockSupport.parkNanos()或LockSupport.parkUntil()方法后，线程会进入该状态。
6.  <mark style="color:blue;">**终止（Terminated）：**</mark>

    可以是线程结束任务之后自动结束，或者产生了异常而结束。

> **wait() 和 sleep() 的区别**
>
> * wait() 是 Object 的方法，而 sleep() 是 Thread 的静态方法；
> * wait() 会释放锁，sleep() 不会。

## 线程的创建

通常，有三种使用线程的方法：

1. 实现Runnable接口
2. 实现Callable接口，作为FutureTask的参数
3. 继承Thread类
