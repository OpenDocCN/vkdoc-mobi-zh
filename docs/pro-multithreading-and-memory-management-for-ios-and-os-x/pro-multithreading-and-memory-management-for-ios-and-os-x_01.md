# 目录

速览目录

关于作者

关于译者

关于技术审校

致谢

引言

![images](img/csquare.jpg)第 1 章：自动引用计数之前的生活

引用计数内存管理概述

进一步探索内存管理

你拥有所创建的任何对象的所有权

你可以使用 `retain` 取得对象的所有权

不再需要时，你必须放弃所拥有的对象的所有权

实现 `alloc`、`retain`、`release` 和 `dealloc`

`alloc` 方法

`retain` 方法

`release` 方法

`dealloc` 方法

Apple 对 `alloc`、`retain`、`release` 和 `dealloc` 的实现

自动释放

自动变量

实现 `autorelease`

Apple 对 `autorelease` 的实现

总结

![images](img/csquare.jpg)第 2 章：ARC 规则

概述

引用计数机制的变化

所有权修饰符

`__strong` 所有权修饰符

`__weak` 所有权修饰符

`__unsafe_unretained` 所有权修饰符

`__autoreleasing` 所有权修饰符

规则

忘记使用 `retain`、`release`、`retainCount` 或 `autorelease`

忘记使用 `NSAllocateObject` 或 `NSDeallocateObject`

遵循对象创建相关方法的命名规则

忘记显式调用 `dealloc`

使用 `@autoreleasepool` 替代 `NSAutoreleasePool`

忘记使用 `zone`（`NSZone`）

C 语言中 `struct` 或 `union` 的成员不能是对象类型变量

`id` 和 `void*` 必须显式转换

属性

数组

总结

![images](img/csquare.jpg)第 3 章：ARC 实现

`__strong` 所有权修饰符

调用 `array` 方法

`array` 方法内部

`__weak` 所有权修饰符

深入探究对象被丢弃时的内部机制

分配新创建的对象

对象的立即释放

自动添加到 `autorelease` 池

`__autoreleasing` 所有权修饰符

`__unsafe_unretained` 所有权修饰符

引用计数

总结

![images](img/csquare.jpg)第 4 章：Blocks 入门

Blocks 初探

匿名函数

变量

Blocks 来救场

Block 字面量语法

Block 类型变量

捕获自动变量

`__block` 说明符

被捕获的自动变量

总结



