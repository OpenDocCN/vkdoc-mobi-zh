# 第 5 章：Block 的实现

探究 Block 的内部机制

源码转换过程

C++中的`this`与 Objective-C 中的`self`

声明`_cself`

`__main_block_impl_0`结构体的构造函数

初始化`__main_block_impl_0`实例

回顾`_NSConcreteStackBlock`

捕获自动变量

匿名函数

可写变量

静态变量与全局变量

`__block`说明符

Block 的内存分区

作为`NSConcreteGlobalBlock`类对象的 Block

堆上的 Block

自动复制 Block

手动复制 Block

多次复制 Block

`__block`变量的内存分区

`__forwarding`机制

捕获对象

何时调用`copy`方法

`__block`变量与对象

Block 的循环引用

复制/释放

本章小结

