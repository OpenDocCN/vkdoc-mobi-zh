# 问题 53：你用过 NSOperationQueue 吗？你能解释一下它吗？

在 iOS 中实现并发操作的一种方式是使用 `NSOperation` 和 `NSOperationQueue` 类。`NSOperationQueue` 管理操作的并发执行。它充当一个优先级队列，使得操作大致按照先进先出（FIFO）的方式执行，优先级更高（`NSOperation.queuePriority`）的操作可以跳到优先级较低的操作前面。`NSOperationQueue` 还可以使用 `maxConcurrentOperationCount` 属性来限制在任何给定时刻执行的最大并发操作数。

