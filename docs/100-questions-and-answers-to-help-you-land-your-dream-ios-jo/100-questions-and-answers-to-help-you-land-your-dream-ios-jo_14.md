# 问题 80：iOS 提供了哪些机制来支持多线程？

在 iOS 中，我们可以使用多种不同的技术来为应用程序提供多线程支持。

*   `NSThread` 创建一个新的低级线程，可以通过调用 `start` 方法来启动。
*   `NSOperationQueue` 允许创建并使用一个线程池来并行执行 `NSOperation`。也可以通过向 `NSOperationQueue` 请求 `mainQueue` 来在**主线程**上运行 `NSOperation`。
*   Grand Central Dispatch (GCD) 是 Objective-C 的一项现代特性，提供了一套丰富的方法和 API 来支持常见的多线程任务。GCD 提供了一种将任务排队以分派到**主线程**、**并发队列**（任务并行运行）或**串行队列**（任务按 FIFO 顺序运行）的方式。

