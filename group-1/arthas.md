# Arthas

官网文档：[https://arthas.aliyun.com/doc/](https://arthas.aliyun.com/doc/)

### **查看最繁忙的线程，以及是否有阻塞情况发生**

场景：我想看下查看最繁忙的线程，以及是否有阻塞情况发生?

```properties
thread -n 3 # 查看最繁忙的三个线程栈信息
thread      # 以直观的方式展现所有的线程情况
thread -b   # 找出当前阻塞其他线程的线程
```

### **确认某个类是否已被系统加载**

场景：我新写了一个类或者一个方法，我想知道新写的代码是否被部署了?

```java
# 找到需要的类全路径(如果存在的话), 会返回类的所有子类
sc *.MathGame

# 查看这个某个类所有的方法
sm demo.MathGame *

# 查看某个方法的信息，如果存在的话
sm demo.MathGame print
```

### **查看一个class类的源码信息(反编译)**

场景：我新修改的内容在方法内部，而上一个步骤只能看到方法，这时候可以反编译看下源码

```java
# 直接反编译出 java 源代码，包含一此额外信息的
jad demo.MathGame
```

### **跟踪某个方法的返回值、入参**

场景：我想看下我新加的方法在线运行的参数和返回值?

{% code overflow="wrap" %}
```java
# 同时监控入参，返回值，及异常
watch demo.MathGame print "{params, returnObj, throwExp}" -e -x 2 
```
{% endcode %}

**查看方法调用栈的信息**

场景：我想看下某个方法的调用栈的信息?

```java
stack demo.MathGame print
```

运行此命令之后需要及时触发方法才会有响应的信息打印在控制台上

### **到最耗时的方法调用**

场景：testMethod这个方法入口响应很慢，如何找到最耗时的子调用?

```java
# run 是 MathGame 中的一个方法
trace demo.MathGame run
```

### **临时更改代码运行**

场景：我找到了问题所在，能否线上直接修改测试，而不需要在本地改了代码后，重新打包部署，然后重启观察效果?

{% code overflow="wrap" %}
```java
# 先反编译出class源码
jad --source-only demo.MathGame > MathGame.java  

# 然后使用外部工具编辑内容
mc MathGame.java -d .  # 再编译成class, -d 指定输出目录

# 最后，重新载入定义的类，就可以实时验证你的猜测了
retransform demo/MathGame.class
```
{% endcode %}

### **测试某个方法的性能问题**

```java
monitor -c 5 demo.MathGame primeFactors
```

### **使用Idea的远程调试**

要让远程服务器运行的代码支持远程调试，则启动的时候必须加上特定的JVM参数，这些参数是：

```properties
-Xdebug -Xrunjdwp:transport=dt_socket,suspend=n,server=y,address=127.0.0.1:5555
```
