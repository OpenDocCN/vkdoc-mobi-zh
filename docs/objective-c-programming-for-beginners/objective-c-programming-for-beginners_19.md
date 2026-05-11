# 第 9 章：Objective-C 高级特性

块（Blocks），在其他编程语言中也称为闭包，是自包含的代码单元，可以传递并在稍后执行。在 Objective-C 中，块是一个语言特性，用于简化异步和基于回调的代码编写。

## 1. 声明和使用块

块使用类似于函数指针的语法声明，但用 `^ { }` 语法括起来。

示例 —— 声明和使用块：

```objective
// 块声明
void (^simpleBlock)(void) = ^{
    NSLog(@"这是一个简单的块");
};

// 调用块
simpleBlock(); // 输出：这是一个简单的块
```

块可以捕获并存储来自封闭作用域的局部变量引用，并在稍后执行代码。

## 2. 带参数和返回值的块语法

块可以接受参数并返回值，从而封装能够以特定输入执行的功能。

示例 —— 带参数和返回值的块：

```objective
// 带参数和返回类型的块声明
NSInteger (^addBlock)(NSInteger, NSInteger) = ^(NSInteger a, NSInteger b) {
    return a + b;
};

// 调用块
NSInteger sum = addBlock(3, 5); // sum 现在为 8
```

块常用于异步任务、枚举、排序中的回调，以及将行为作为参数传递给方法或函数。

## Grand Central Dispatch（GCD）

Grand Central Dispatch（`GCD`）是 Apple 提供的一个强大 API，用于在多核硬件上以线程安全的方式实现并发编程。它通过为异步管理任务提供高级抽象，简化了多线程应用程序的实现。

### 1. 调度队列

调度队列是 `GCD` 的核心，负责异步或并发执行任务。

示例 —— 创建和使用调度队列：

```objective
// 创建串行调度队列
dispatch_queue_t serialQueue = dispatch_queue_create("com.example.serialQueue", DISPATCH_QUEUE_SERIAL);

// 异步调度任务
dispatch_async(serialQueue, ^{
    NSLog(@"任务 1 在串行队列上运行");
});

// 创建并发调度队列（全局队列）
dispatch_queue_t concurrentQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

// 异步调度任务
dispatch_async(concurrentQueue, ^{
    NSLog(@"任务 2 在并发队列上运行");
});
```

串行队列按添加顺序一次执行一个任务，而并发队列则并发执行任务，并根据系统资源管理线程执行。

### 2. 同步和异步调度任务

任务可以同步调度（阻塞当前线程直到任务完成）或异步调度（在后台执行任务而不阻塞当前线程）。

示例 —— 同步和异步调度任务：

```objective
// 同步调度任务
dispatch_sync(serialQueue, ^{
    NSLog(@"串行队列上的同步任务");
});

// 异步调度任务
dispatch_async(concurrentQueue, ^{
    NSLog(@"并发队列上的异步任务");
});
```

对于必须在继续执行前完成的任务，使用同步调度；对于非阻塞的后台任务，使用异步调度。

### 3. 延迟调度

GCD 允许使用 `dispatch_after` 在指定延迟后执行任务。

示例 —— 延迟调度：

```objective
// 延迟调度任务
double delayInSeconds = 2.0;
dispatch_time_t delay = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds * NSEC_PER_SEC));
dispatch_after(delay, dispatch_get_main_queue(), ^{
    NSLog(@"延迟任务在 2 秒后执行");
});
```

适用于实现动画、重试机制或延迟非关键任务。

## 最佳实践

- **避免保留环**：在块中捕获对象时，使用 `__weak` 或 `__block` 限定符以避免保留环，尤其是在块内引用 `self` 时。
- **使用主队列进行 UI 更新**：在主队列（`dispatch_get_main_queue()`）上执行 UI 更新，以确保 UI 变化安全且响应迅速。
- **分析与监控**：使用 Xcode Instruments 分析使用 `GCD` 时的应用程序性能，以识别瓶颈并优化资源使用。

块和 Grand Central Dispatch（`GCD`）是现代 Objective-C 开发中不可或缺的部分，使开发者能够编写高效的异步和并发代码。



