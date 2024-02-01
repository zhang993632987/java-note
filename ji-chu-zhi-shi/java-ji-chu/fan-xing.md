# 泛型

<mark style="color:blue;">**泛型的本质是参数化类型，泛型提供了编译时类型的安全检测机制，该机制允许程序在编译时检测非法的类型。**</mark>

## 类型擦除 <a href="#jian-shu-fan-xing-ca-chu" id="jian-shu-fan-xing-ca-chu"></a>

<mark style="color:blue;">**在编码阶段采用泛型时加上的类型参数，会被编译器在编译时去掉，这个过程就被称为类型擦除。**</mark>因此，泛型主要用于编译阶段，在编译后生成的 Java 字节文件中不包含泛型的类型信息。

{% hint style="success" %}
Java 类型的擦除过程为：

1. 首先，查找用来替换类型参数的具体类（该具体类一般为 Object），如果指定了类型参数的上界，则以该上界作为替换时的具体类；
2. 然后，把代码中的类型参数都替换为具体的类。
{% endhint %}
