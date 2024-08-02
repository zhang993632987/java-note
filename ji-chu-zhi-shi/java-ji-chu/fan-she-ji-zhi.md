# 反射机制

反射机制指**在程序运行过程中，对任意一个类都能获取其所有属性和方法**，并且**对任意一个对象都能调用其任意一个方法**。这种**动态获取类和对象的信息**，以及**动态调用对象的方法**的功能被称为 Java 语言的反射机制。

## Class 对象 <a href="#jian-shu-java-zhong-class-dui-xiang" id="jian-shu-java-zhong-class-dui-xiang"></a>

java 中对象可以分为**实例对象**和 **Class 对象，每一个类都有一个 Class 对象**，其包含了与该类有关的信息。

获取 Class 对象的方法：

```java
Class.forName(“类的全限定名”)
实例对象.getClass()
类名.class
```

## 反射的使用

在 Java 中，**Class 类与 java.lang.reflect 类库一起对反射技术进行支持**。

在反射包中，常用的类主要有：

* **Constructor** 类表示的是 Class 对象所表示的类的构造方法，利用它可以在运行时动态创建对象
* **Field** 类表示 Class 对象所表示的类的成员变量，通过它可以在运行时动态修改成员变量的属性值（包括 private 成员变量）
* **Method** 类表示 Class 对象所表示的类的成员方法，通过它可以动态调用对象的方法（包括 private 方法）
