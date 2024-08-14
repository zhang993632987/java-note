# 移位运算符

java 中有三种移位运算符：

* <mark style="color:blue;">**<<：**</mark>左移运算符。x << 1，相当于 x 乘以 2（不溢出的情况下），低位补 0
* <mark style="color:blue;">**>>：**</mark>**带符号右移**。x >> 1，相当于x 除以 2，正数高位补 0，负数高位补 1
* <mark style="color:blue;">**>>>：**</mark>**无符号右移**。忽略符号位，空位都以 0 补齐
