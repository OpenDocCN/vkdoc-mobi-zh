# Objective-C 线程

Objective-C 线程的创建十分简便，因为线程的代码可以是任意对象的任意方法。在 Java 中，包含代码的对象必须实现 `Runnable` 接口，并在其 `run()` 方法中实现代码。而 Objective-C 仅有的要求是：包含线程代码的方法需接受一个对象参数（可忽略）并返回 void。清单 15-1 展示了如何启动一个线程。

**清单 15-1.** 启动一个线程

Java

```java
public class Runner implements Runnable

{

    public void run()

    {

        System.out.println("使用线程运行。");

    }

}

…

Runner runner = new Runner();

Thread thread = new Thread(runner);

thread.start();
```

Objective-C

```objc
@implementation Runner

- (void)runMe:(id)ignored

{

    NSLog(@"使用线程运行。");

}

@end

…

Runner *runner = [Runner new];

[NSThread detachNewThreadSelector:@selector(runMe:)

                     toTarget:runner

                   withObject:nil];
```

**主线程**

主线程是进程创建时启动的第一个线程，具有特殊性。所有用户输入和事件都会被发送到主线程上运行的事件循环。所有用户界面的更改都必须在主线程上进行；用户界面框架并非线程安全。从任何实际意义上讲，应用程序的进程就是主线程。当主线程结束时，进程会终止——并立即杀死所有其他线程。

有许多方法可以引用、定位、检测或返回主线程。这些方法允许你在必要时轻松定位主线程或其事件循环。

**管理线程**

关于管理线程的最佳建议是“不要管理”。线程对微管理反应不佳，这会使你的代码变得脆弱且可移植性差。最重要的是，不要试图猜测内核的线程调度器，也不要试图强制其表现出特定行为。随着内核线程调度变得越来越复杂，用于操作运行线程的应用程序编程接口数量已经减少。例如，Objective-C 没有 `yield()` 的等价方法，而 `java.lang.Thread` 中的 `stop()`、`suspend()` 和 `resume()` 方法均已弃用。

线程应围绕定义清晰、自洽的任务来组织。它们应尽量减少通信，并使用信号量与其他线程协调工作。除此之外，你只需让线程在有工作时运行，没有工作时挂起即可。如果你发现自己不得不在代码中插入睡眠语句或创建信号量来强制内核切换任务，那么你应该重新审视任务的设计。

话虽如此，在某些特殊情况下，你可能需要更改任务属性或使其休眠。接下来将说明如何使线程休眠。线程属性将在后续小节中讨论。

**使线程休眠**

使线程休眠一段时间是少数保留下来的线程状态操作之一。

使线程休眠会挂起该线程，直到指定的时间间隔过去。之后，该线程会再次变为“可运行”状态，内核会将其重新加入正在运行的线程轮转中。换句话说，并不保证线程在时间间隔过后会立即恢复运行；只是在时间间隔过去之前它不会再次运行而已。

`NSThread` 的 `+sleepUntilDate:` 和 `+sleepForTimeInterval:` 方法会导致当前线程无条件地休眠给定的时间长度。这可以是一个未来的 `NSDate` 对象，也可以是一个以秒为单位的 `NSTimeInterval` 值。日期和时间间隔使用双精度浮点值来表示秒，因此可以指定纳秒级的时间间隔。内核不保证时间的精确度，只保证在该时间过去之前线程不会再次执行。



还有许多方法（大部分将在后续章节中介绍）会条件性地让线程休眠一段时间。这些消息接受一个 `NSDate` 对象，用于指定未来放弃等待条件并恢复执行的时间。最简单的例子是 `-[NSLock lock]` 和 `-[NSLock lockBeforeDate:(NSDate*)limit]` 这两个方法。两者都尝试获取 `NSLock` 信号量上的锁。`-lock` 方法会一直等待直到获得锁。相比之下，`-lockBeforeDate:` 方法会在获得锁或达到 `NSDate` 指定的时间时返回。

> **提示：** 要创建一个代表未来 2.5 秒时间的 `NSDate` 对象，请使用 `[NSDate dateWithTimeIntervalSinceNow:2.5]`。要获取一个永远不会被达到的 `NSDate` 对象，请使用 `[NSDate distantFuture]`。

当线程需要按固定时间间隔执行操作时，应将其置于休眠状态。清单 15-2 展示了一个简单的类，它运行一个“心跳”线程，该线程每秒最多更新进度指示器两次。

**清单 15-2. 心跳线程**

```objc
@interface Process : NSObject

@property double progress;

@end

@interface Heartbeat : NSObject {
    NSThread *thread;
    Process *monitor;
    NSProgressIndicator *indicator;
}

+ (Heartbeat*)startHeartbeatProcess:(id)process
                  withIndicator:(NSProgressIndicator*)progress;
- (void)stop;
- (void)heartbeatThread:(id)ignored;
- (void)updateIndicator;

@end

…

@implementation Heartbeat

+ (Heartbeat*)startHeartbeatProcess:(id)process
                  withIndicator:(NSProgressIndicator*)progress
{
    Heartbeat *heartbeat = [Heartbeat new];
    heartbeat->monitor = process;
    heartbeat->indicator = progress;
    heartbeat->thread = [[NSThread alloc] initWithTarget:heartbeat
                                                selector:@selector(heartbeatThread:)
                                                  object:nil];
    [heartbeat->thread start];
    return heartbeat;
}

- (void)stop
{
    [thread cancel];
}

- (void)heartbeatThread:(id)ignored
{
    while (![thread isCancelled]) {
        [self performSelectorOnMainThread:@selector(updateIndicator)
                               withObject:nil
                            waitUntilDone:YES];
        [NSThread sleepForTimeInterval:0.5];
    }
}

- (void)updateIndicator
{
    [indicator setDoubleValue:monitor.progress];
}

@end
```

消息 `+startHeartbeatProcess:withIndicator:` 会创建一个 `Heartbeat` 对象，并在新线程中启动其 `-heartbeatThread:` 方法。大约每半秒钟，该线程就会将一个 `-updateIndicator` 消息排入主线程执行队列。线程会一直运行，直到 `Heartbeat` 对象收到 `-stop` 消息。

请注意，清单 15-2 中的示例远非线程的最有效用法。虽然它能工作，但本章末尾的“定时器”部分提供了一个更合理的解决方案。

#### 线程属性

线程对象拥有许多关联的、能提供相关信息的属性。尽管存在关于过度线程管理的注意事项，但仍有一些线程属性你可能需要修改。有些属性只能在线程启动前设置，而另一些则只能在启动后设置。

##### 信息

线程信息属性列在表 15-2 中。你可以使用它们来获取相关的 `NSThread` 对象或检查线程的状态。类方法返回全局值或关于当前执行线程的信息。实例方法可以从任何线程发送给任何 `NSThread` 对象。

**表 15-2. 信息性线程属性**

| `NSThread` 方法 | 描述 |
| :--- | :--- |
| `+currentThread` | 当前执行线程的 `NSThread` 对象 |
| `+mainThread` | 主线程的 `NSThread` 对象 |
| `+isMainThread` | 如果当前线程是主线程，则返回 `YES` |
| `+isMultiThreaded` | 如果除了主线程之外，还有另一个线程曾经由 `NSThread` 启动，则返回 `YES` |
| `-isMainThread` | 如果该线程对象代表主线程，则返回 `YES` |
| `-isExecuting` | |



