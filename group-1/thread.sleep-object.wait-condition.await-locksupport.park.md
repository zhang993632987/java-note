# Thread.sleep()、Object.wait()、Condition.await()、LockSupport.park()

## **Thread.sleep()和Object.wait()的区别**

1. Thread.sleep()不会释放占有的锁，Object.wait()会释放占有的锁；
2. Thread.sleep()必须传入时间，Object.wait()可传可不传，不传表示一直阻塞下去；
3. Thread.sleep()到时间了会自动唤醒，然后继续执行；
4. Object.wait()不带时间的，需要另一个线程使用Object.notify()唤醒；
5. Object.wait()带时间的，假如没有被notify，到时间了会自动唤醒；

这两个方法最大的区别是Thread.sleep()不会释放锁资源，Object.wait()会释放锁资源。

## **Object.wait()和Condition.await()的区别**

Object.wait()和Condition.await()的原理是基本一致的，不同的是Condition.await()底层调用LockSupport.park()方法实现阻塞，Condition.await()在阻塞当前线程之前干了两件事：

* 一是把当前线程添加到条件队列中
* 二是“完全”释放锁，也就是让state状态变量变为0。

## **Thread.sleep()和LockSupport.park()的区别**&#x20;

1. 从功能上来说，Thread.sleep()和LockSupport.park()方法类似，都是阻塞当前线程的执行，且都不会释放当前线程占有的锁资源；
2. Thread.sleep()没法从外部唤醒，只能自己醒过来；LockSupport.park()方法可以被另一个线程调用LockSupport.unpark()方法唤醒；
3. Thread.sleep()方法声明上抛出了InterruptedException中断异常，所以调用者需要捕获这个异常或者再抛出；LockSupport.park()方法则不需要捕获中断异常；
4. Thread.sleep()本身便是一个native方法；而LockSupport.park()底层调用Unsafe的native方法；

## **Object.wait()和LockSupport.park()的区别**

1. Object.wait()方法需要在synchronized块中执行；LockSupport.park()可以在任意地方执行；
2. Object.wait()方法声明抛出了中断异常，调用者需要捕获或者再抛出；LockSupport.park()不需要捕获中断异常；
3. Object.wait()不带超时的，需要另一个线程执行notify()来唤醒，但不一定继续执行后续内容；
4. LockSupport.park()不带超时的，需要另一个线程执行unpark()来唤醒，一定会继续执行后续内容；

> **park()/unpark()底层的原理是“二元信号量”，可以把它想象成只有一个许可证的Semaphore，只不过这个信号量在重复执行unpark()的时候不会增加许可证数量，最多只有一个许可证。**
