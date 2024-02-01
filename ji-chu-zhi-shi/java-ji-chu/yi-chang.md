# 异常

## 异常的分类 <a href="#jian-shu-java-yi-chang-de-fen-lei" id="jian-shu-java-yi-chang-de-fen-lei"></a>

Java 中所有的异常 Exception 和错误 Error 有一个共同的祖先 Throwable，其中：

* <mark style="color:blue;">**Error**</mark> 表示虚拟机内部错误，例如 OutOfMemoryError、StackOverflowError。
* <mark style="color:blue;">**Exception**</mark> 又分为两类：
  * **非检查型异常**：**所有 RuntimeException 的子类**均是非检查异常。**这些异常一般是由程序逻辑错误引起的。对于非检查异常，并不强制要求必须进行处理。**例如：NullPointerException、NumberFormatException、IllegalArgumentException、IndexOutOfBoundsException 等。
  * **检查型异常**：**在所有 Exception 的子类中，排除掉 RuntimeException 及其子类，剩下的所有异常均为检查型异常**。与非检查型异常不同，检查型异常必须被得到处理，否则编译器会报错。例如：InterruptedException、IOException、FileNotFoundException。

## throw 与 throws 的区别 <a href="#jian-shu-throw-yu-throws-de-qu-bie" id="jian-shu-throw-yu-throws-de-qu-bie"></a>

* throw 一般是用在方法体的内部，由开发者定义当程序语句出现问题后主动抛出一个异常。
* throws 一般用于方法声明上，代表该方法可能会抛出的异常列表。

{% hint style="success" %}
## 异常表

**Java 利用异常表来实现异常处理逻辑。**

异常表中包含了一个或多个异常处理器(Exception Handler)的信息，这些信息包含如下

* **from** 可能发生异常的起始点
* **to** 可能发生异常的结束点
* **target** 上述 from 和 to 之间发生异常后的异常处理器的位置
* **type** 异常处理器处理的异常的类信息
{% endhint %}
