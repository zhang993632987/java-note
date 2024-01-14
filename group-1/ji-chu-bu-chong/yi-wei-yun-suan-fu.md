# 移位运算符

java中有三种移位运算符：

* <mark style="color:blue;">**<<：**</mark>左移运算符。x << 1，相当于x乘以2(不溢出的情况下)，低位补0
* <mark style="color:blue;">**>>：**</mark>**带符号右移**。x >> 1，相当于x除以2，正数高位补0，负数高位补1
* <mark style="color:blue;">**>>>：**</mark>**无符号右移**。忽略符号位，空位都以0补齐
