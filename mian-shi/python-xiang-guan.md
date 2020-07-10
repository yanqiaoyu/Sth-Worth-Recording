# Python相关

## 1.讲讲迭代器/生成器/装饰器

迭代器：

这里我们先来回顾一下**什么是可迭代对象（Iterable）**？

可以**直接作用于for循环的对象**统称为**可迭代对象**，即Iterable。 　　\# 一是集合数据类型，如list、tuple、dict、set、str等； 　　\# 二是generator，包括生成器和带yield的generator function。

那么**什么又是迭代器（Iterator）？**

可以**被next\(\)函数调用**并**不断返回下一个值**（直到**没有数据时抛出StopIteration错误**）的对象称为迭代器，即Iterator。

生成器：



装饰器：

返回值是函数的高阶函数，当我们不想修改某个func里面的源码的时候，就可以为这个func添加一个修饰器，主要的应用场景有 添加日志，打印程序执行时间，

