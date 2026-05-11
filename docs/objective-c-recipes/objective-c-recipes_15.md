# 自动引用计数 (ARC)

`自动引用计数`（`Automatic Reference Counting`，简称 `ARC`）是一种编译器级别的系统，可自动执行引用计数的过程。`ARC` 自 `iOS 5` 和 `Mac OSX 10.6` 起可用，其中 `iOS` 使用 `Xcode 4.2`，`Mac` 使用 `Xcode 4.3`。本书中的示例大多使用 `ARC` 编写。

`ARC` 本质上是自动完成了你在本章中将要手动执行的操作。在你的 `Objective-C` 程序编译之前，所有实现引用计数所需的代码都会被自动插入到程序中。

