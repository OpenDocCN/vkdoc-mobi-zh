# NSLocalized

简要说明`FailureReason`（`FailureReasonErrorKey`）对应的失败原因。可选属性。并非所有错误对话框都会显示该属性。

`-localizedRecoverySuggestion`（`NSLocalizedRecoverySuggestion`，`RecoverySuggestionErrorKey`）：描述用户可采取哪些操作来从错误中恢复的句子。可选属性。

`-localizedRecoveryOptions`（`NSLocalizedRecoveryOptions`，`RecoveryOptionsErrorKey`）：一个按钮名称数组，对应恢复建议中描述的用户选项。如果为`nil`，则唯一选项为“OK。”

一个`NSError`对象可以提供恢复尝试对象，并且如果错误包含恢复选项，则应提供该对象。`-presentError:`消息会将用户的恢复选择传递给恢复尝试对象，该对象需执行用户的意愿。恢复尝试对象必须实现非正式协议`NSErrorRecoveryAttempting`中的某个方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 14 章 ■ 异常处理

### 结合错误与异常

学术界关于异常与传统错误处理孰优孰劣的争论仍将持续。与此同时，我个人非常推崇将两者结合。`NSError`对象在需要本地化的复杂应用程序中具有明显优势，而 Java 风格的异常处理在简化代码和执行流程方面则优势显著。清单 14-10 展示了如何将传统的 C 语言错误处理、`NSError`对象和异常结合起来。

**清单 14-10.** 混合错误处理

```
@try {
    const char *path = …
    int fd = open(path, O_RDONLY);
    if (fd < 0) {
        NSString *errorPath = [NSString stringWithCString:path];
        NSDictionary *info = [NSDictionary dictionaryWithObject:errorPath forKey:@"Path"];
        NSError *error = [NSError errorWithDomain:NSPOSIXErrorDomain code:errno userInfo:info];
        @throw error;
    }
    …
    close(fd);
} @catch (NSError *error) {
    [self presentError:error];
}
```

### 总结

Objective-C 的异常处理强大、简单，与 Java 提供的异常处理不相上下。通过大量使用断言，可以约束 Objective-C 的许多许可性。POSIX、BSD、Core Foundation 以及大多数 Cocoa 方法仅在（被视为）运行时错误或编程错误时才使用异常。你可以接纳这一理念，或者通过将异常与传统的设计模式结合，继续使用异常进行编程。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

Java 是最早直接支持线程和线程同步的编程语言之一。这是一个非常有远见的决定。多核、多处理器计算机系统已成为常态，而非例外。高效的多线程编程现在对于有效的程序开发至关重要，并且这一趋势仍在加速。

线程和信号量在概念上很简单，Java 与 Objective-C 之间的高层差异微不足道。Objective-C 还广泛使用了运行循环，它可以将线程转变为事件处理器。除了语言内置的基本`@synchronized`指令外，Cocoa 框架还提供了许多额外的同步工具，每个工具都针对特定需求而量身定制。最后，还有一些类可以执行延迟操作、为您管理独立的线程以及执行定时任务，从而减轻您创建和管理自己线程的负担。

本章首先描述如何在 Objective-C 中创建、运行和销毁线程。同时解释运行循环，并说明为什么需要创建一个运行循环以及如何创建。之后，详细介绍同步多个线程以及使代码线程安全的多种方式。最后，介绍一些有用的类，它们可以使线程管理更简单，甚至可能无需管理。

### 线程 API

线程管理和控制以 Objective-C 类`NSThread`为核心，与 Java 的`java.lang.Thread`类非常相似。对应的方法列于表 15-1 中。

**表 15-1.** 等效的线程方法

| Java (`java.lang.Thread`) | Objective-C (`NSThread`) | 描述 |
|---|---|---|
| `currentThread()` | `+currentThread` | 当前执行线程的线程对象 |
| `start()` | `-start` | 启动一个线程 |
| `run()` | `-main` | 要执行的代码 |
| `isAlive()` | `-isExecuting`, `-isFinished` | 判断线程是否已启动或已结束 |
| `sleep(long)` | `+sleepUntilDate:`, `+sleepForTimeInterval:` | 将线程挂起一段时间 |
| `getPriority()` | `+threadPriority` | 线程的优先级 |
| `setPriority(int)` | `+setThreadPriority` | 更改线程的优先级 |
| `getName()` | `-name` | 线程的名称 |
| `setName(String)` | `-setName:` | 设置线程的名称 |
| `getStackTrace()` | `+callStackReturnAddresses` | 获取堆栈上的返回地址数组 |

以下是 Objective-C 与 Java 中线程的关键区别：

- Objective-C 中没有线程组。
- Objective-C 接口没有提供获取现有线程列表的方法。
- 没有安全模型。某些操作（例如设置线程优先级）只能从当前运行的线程执行，而在 Java 中则可以在任意线程上执行。
- 不能中断线程。`NSThread`确实提供了一个可以设置和测试的`canceled`属性，但取消线程不会解除其阻塞状态。
- 没有守护线程。只要主线程在运行，Objective-C 进程就会持续运行，并且当进程终止时，所有线程都会终止。
- 没有`join()`函数，但可以使用`NSConditionLock`轻松实现相同的功能，本章稍后将对此进行说明。
- 没有`yield()`方法或任何等效方法。

Objective-C 线程的生命周期与 Java 中的大致相同。您创建一条线程，该线程将执行一个对象的方法。当方法返回时，线程被终止并销毁。与共享给其他线程的任何对象的交互都必须使用信号量进行协调。

如果您对线程的需求仅此而已，请阅读“启动线程”和“线程同步”部分。如果您想考虑已经为您管理线程的现成类，请查看“操作”部分。本章其余部分主要探讨线程中较为深奥的细节，这些细节对大多数程序员来说关系不大。

### 启动线程

在 Objective-C 中启动新线程甚至比在 Java 中还要简单——考虑到 Java 中创建线程已经相当简单，这足以说明问题。在 Objective-C 中创建线程有三种方法：

- 通过发送消息`+detachNewThreadSelector:toTarget:withObject:`创建并立即启动一个线程。该消息指定了包含线程代码的对象和方法，以及一个可选参数，该参数将在线程开始执行时传递给线程。
- 使用`-initWithTarget:selector:object:`创建一个新的`NSThread`对象。这与第一种技术相同，区别在于线程不会启动。您可以在使用`-start`消息启动线程之前自定义该线程对象。
- 定义您自己的`NSThread`子类，重写`-main`方法，并使用`-init`创建您的实例。这相当于继承`java.lang.Thread`并重写`run()`。当对象收到`-start`消息时，它会创建一个新线程，并在新线程中执行其`-main`方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

> **注意** 创建并返回`NSThread`对象的构造器是操作系统中相当新的添加。在早于 10.5 版本的 Mac OS X 中，只有`+detachNewThreadSelector:toTarget:withObject:`可以创建新线程。您不能继承`NSThread`或在启动前自定义线程。



