# 线程

若要更深入一些，可以使用类似于代码清单 15-4 所示的代码。这第二个示例使用你自己的循环来“驱动”运行循环。`-runMode:beforeDate:` 消息会使线程挂起，直到处理完单个事件或到达 `beforeDate` 指定的时间。这允许你的代码在每次事件之间或定期执行操作，并且你还可以测试任何可能用于停止运行循环或线程的全局条件。

**代码清单 15-4.** 启动一个运行循环

```
static BOOL keepRunning = YES;
…
while (keepRunning
       && [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]]) {
  …
}
```

最后，`-[NSConnection runInNewThread]` 便捷函数完成了启动分布式对象服务器所需的所有工作：它创建并启动一个新线程，将 `NSConnection` 的端口添加到新线程的运行循环中，并向该运行循环发送一条 `-run` 消息。

#### 运行循环模式

运行循环在模式下运行。运行循环的输入源具有模式。运行循环只处理与其当前模式一致的输入源中的事件。通常你不需要了解关于运行循环模式的任何信息，只需要知道它们存在即可。基本的运行循环模式在表 15-3 中列出。

**表 15-3.** 运行循环模式

| 模式 | 描述 |
|------|-------------|
| `NSDefaultRunLoopMode` | 这是默认模式，也是不接受模式参数的运行循环方法使用的模式。 |
| `NSModalPanelRunLoopMode` | 只处理与模态对话框相关的事件。 |
| `NSEventTrackingRunLoopMode` | 只处理与活跃鼠标追踪相关的事件。 |
| `NSConnectionReplyMode` | 只处理分布式对象回复事件。 |

运行循环模式用于推迟那些不适合当前环境的事件。

顶层运行循环总是在 `NSDefaultRunLoopMode` 模式下运行，处理所有事件。如果用户界面导致显示一个模态对话框，AppKit 框架将启动一个在 `NSModalPanelRunLoopMode` 模式下运行的嵌套运行循环。这会忽略所有与模态对话框无关的事件，实质上冻结了应用程序的其余部分。类似地，当鼠标点击/拖动开始时，它会使用 `NSEventTrackingRunLoopMode` 进行追踪。当你向一个远程对象发送消息时，会使用 `NSConnectionReplyMode`。远程对象会启动一个嵌套运行循环，忽略所有排队的输入源事件，只处理来自远程对象的回复。

#### 停止运行循环

停止运行循环实际上有点棘手。之前，我提到运行循环只要有输入源就会一直运行。这没错，但各种晦涩的特性会向你的运行循环添加自定义输入源。许多这样的输入源会持续很长时间，甚至永远存在，从而使运行循环无限期地保持活动状态。最直接的解决方案是设计你的线程，使其不需要停止运行循环。让线程在不做任何有用的事情时空闲，并在应用程序终止时消失。对于分布式对象，当你想要停止共享你的根分布式对象时，请关闭 `NSConnection` 对象。你始终可以重新注册连接或设置一个新的根对象来恢复该服务。

如果你必须停止一个运行循环，有三种基本技术：

- 使用类似于代码清单 15-4 中的代码来定期检查运行循环是否应该停止。问题是循环在每个事件之后或超时之后只会执行一次。你不希望将超时时间设置得太短——这构成了轮询——因为这会浪费资源。
- 调用 Core Foundation 函数 `CFRunLoopStop()`，并将通过 `-[NSRunLoop getCFRunLoop]` 获取的 `CFRunLoopRef` 值传递给它。
- 创建一个自定义的运行循环输入源，并将其与代码清单 15-4 中的技术结合使用。自定义输入源的事件处理器可以设置一个标志，指示运行循环应该停止，这将立即导致 `-runUntilDate:` 返回，然后你的外层循环就可以退出。

#### 自定义运行循环

`NSRunLoop` 类是对 Core Foundation 运行循环函数和类型的一个简单封装。

如果你想更深入地研究运行循环，可能开发你自己的输入源，请阅读《线程编程指南》¹ 中的“运行循环管理”部分。

### 线程通知

`NSThread` 发送两个通知，在表 15-4 中列出。有关通知的更多信息，请参见第 18 章。

**表 15-4.** 线程通知

| 通知 | 描述 |
|--------------|-------------|
| `NSThreadWillExitNotification` | 在线程代码返回之后，但在线程被销毁之前发送。此通知会发送给即将被销毁的线程上的所有观察者。 |
| `NSWillBecomeMultiThreadedNotification` | 在进程中创建第一个新的（非主）线程之前发送给所有观察者。该通知在主线程上发送，并且只发送一次。 |

回到第 7 章，我介绍了一个可以在线程安全环境中选择性运行的 FIFO 类。让我们稍微修改一下这个类——使其真正自动化——通过重写其 `-init` 方法并添加一个通知观察者，如代码清单 15-5 所示。

**代码清单 15-5.** 对自动线程安全 FIFO 类的补充

```objc
@implementation AutoSafeFIFO

- (id) init
{
  self = [super init];
  if (self != nil) {
    stack = [NSMutableArray new];
    if ([NSThread isMultiThreaded]) {
      [self makeThreadSafe];
    } else {
      NSNotificationCenter *center = [NSNotificationCenter defaultCenter];
      [center addObserver:self
                selector:@selector(willBecomeMultiThreadedNotification)
                    name:NSWillBecomeMultiThreadedNotification
                  object:nil];
    }
  }
  return self;
}

- (void)willBecomeMultiThreadedNotification:(NSNotification*)notification
{
  [self makeThreadSafe];
}

…
@end
```

对象的构造器会判断应用程序是否已经在多线程下运行。如果是，它会使自身成为线程安全的。如果不是，它会订阅 `NSWillBecomeMultiThreadedNotification`。如果在 FIFO 创建后的任何时间创建了第二个线程，FIFO 对象会自动切换到线程安全模式。

### 线程同步

两个或多个独立线程对对象的并发访问，在几乎所有编程语言中都带来一系列相同的问题。Java 和 Objective-C 都通过 `@synchronized`（`synchronized`）指令提供了语言支持。任何标记为 `@synchronized` 的代码块都会被保护起来，防止同时被多个线程执行。

本节将回顾 Objective-C 中的同步支持。除了 `@synchronized` 指令之外，Cocoa 框架还提供了几个实用程序类，它们实现了不同类型的互斥信号量、一组用于组织并发任务的类以及定时器。

#### `@synchronized` 指令

Objective-C 的 `@synchronized` 指令与 Java 的 `synchronized` 关键字几乎完全相同，只有一个很小的例外：`@synchronized` 不能用作方法修饰符。要获得等效的功能，请使用 `@synchronized(self)` 作为方法的最外层代码块，如代码清单 15-6 所示。

**代码清单 15-6.** 同步方法

**Java**

```java
public class Timing
{
  public synchronized void safe( )
  {
    // 线程安全代码
  }
}
```

**Objective-C**

```objc
@interface Timing : NSObject
- (void)safe;
@end

@implementation Timing
- (void)safe
{
  @synchronized(self) {
    // 线程安全代码
  }
}
@end
```

除了这个微小的限制之外，你可以像在 Java 中使用 `synchronized` 一样使用 `@synchronized`。

#### 互斥信号量对象

¹ Apple 公司，《线程编程指南》，http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/，2008 年。



Java 5.0 引入了一系列新的并发流程控制类，包括各种互斥信号量、队列、资源计数器等。Objective-C 一直拥有`NSLock`、`NSRecursiveLock`和`NSConditionLock`类。操作系统的较新版本增加了`NSOperation`用于管理复杂的任务集。`NSOperation`将在下一节讨论。

这三个`NS…Lock`类各自提供了一种不同类型的互斥信号量，简称 mutex。它们都通过获取锁来工作，这大致相当于进入一个`@synchronized`代码块。所有其他尝试获取同一对象锁的线程将被挂起，直到原始锁被释放。一旦信号量解锁，其他线程中的一个将获得锁并恢复执行。

所有互斥信号量类都有几个共同点：

- 它们都遵循`NSLocking`协议，该协议定义了基本的`-lock`和`-unlock`方法。
- 它们都实现了`-lockBeforeDate:`和`-tryLock`方法。
- 它们都实现了`-name`和`-setName:`方法。

`-tryLock`和`-lockBeforeDate:`消息尝试获取锁，如果成功则返回`YES`。`-tryLock`立即返回，而`-lockBeforeDate:`会挂起，直到获取锁或`NSDate`对象指定的时间到来。这是使用`NS…Lock`对象相比`@synchronized`指令的显著优势之一。

最后，你可以出于任何原因给你的锁命名。

#### NSRecursiveLock

`NSRecursiveLock`对象的行为与`@synchronized`块非常相似。`NSRecursiveLock`是线程之间的互斥信号量，但在单个线程内，它可以被锁定任意多次。锁在初始的`-lock`消息期间被获取一次，相当于进入一个`@synchronized`块。之后在同一个线程上发送的`-lock`消息会递增一个计数器。一旦锁为每个`-lock`消息接收到一个`-unlock`消息，锁就会被释放。

一个显著的区别是，`@synchronized`块会在重新抛出异常之前自动捕获异常并释放锁。如果你在可能抛出异常的代码周围使用`NSRecursiveLock`对象，请确保捕获异常并清理你的锁。

#### NSLock

`NSLock`是最简单的互斥信号量，只实现了基本的`-lock`、`-tryLock`、`-lockBeforeDate:`和`-unlock`方法。在`@synchronized`指令被添加之前编写的 Objective-C 代码很可能使用`NSLock`对象而不是`@synchronized`块，尽管`@synchronized`的行为更接近于`NSRecursiveLock`。

> **注意：** `NSLock`必须始终从锁定它的同一个线程解锁。一些程序员为了试图实现跨线程同步，会在同一个线程上两次锁定一个`NSLock`对象——导致死锁——然后通过从另一个线程解锁该对象来释放它。这不能保证有效，并可能导致致命的程序错误。正确的解决方案是使用`NSConditionLock`，稍后描述。

`NSLock`的主要优势在于速度和简单性。与`NSRecursiveLock`不同，一个`NSLock`在再次解锁之前只能被获取一次。考虑清单 15-7 中的代码。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 清单 15-7. 递归互斥信号量

```
@interface ZombieController : NSObject {
    NSMutableArray *nearbyZombies;
    double totalDamage;
    NSLock *lock;
}
- (void)inflictDamage:(double)damage onZombieAtIndex:(NSUInteger)index;
- (void)detonateFlashBomb;
@end

@implementation ZombieController
- (id)init
{
    self = [super init];
    if (self != nil) {
        lock = [NSLock new];
        …
    }
    return self;
}

- (void)inflictDamage:(double)damage onZombieAtIndex:(NSUInteger)index
{
    [lock lock];
    Zombie *zombie = [nearbyZombies objectAtIndex:index];
    [zombie inflictDamage:damage];
    totalDamage += damage;
    [lock unlock];
}

- (void)detonateFlashBomb
{
```



```  
// 对所有附近的僵尸造成 10 点伤害  
NSUInteger i;  

[lock lock];  

for (i=0; i<[nearbyZombies count]; i++) {  
[self inflictDamage:10.0 onZombieAtIndex:i];  
}  

[lock unlock];  
}  

@end  
```  

[www.it-ebooks.info](http://www.it-ebooks.info/)  

## 第 15 章 ■ 线程  

清单 15-7 中的代码在收到 `detonateFlashBomb` 消息时会发生死锁（即卡死，永远无法继续运行）。`detonateFlashBomb` 中的 `[lock lock]` 语句会获取 `NSLock`，从而阻止其他线程执行该方法，直到该方法执行完毕。当对象向自身发送 `inflictDamage:onZombieAtIndex:` 方法时，第二次尝试获取锁会使线程永久挂起，因为它要等待自己释放最初的锁——而这一释放永远不会发生。  

可以通过将 `NSLock` 替换为 `NSRecursiveLock`，或者重写代码使用 `@synchronized` 指令来修复此问题。  

## NSConditionLock  

`NSConditionLock` 类的作用类似于 `NSLock`，但增加了一个条件属性。该条件是一个任意的整型状态值。当你请求获取对象上的锁时，可以指定必须满足的条件值，锁才会被获取。这提供了一种简单、灵活且高效的方式来协调线程间的状态信息或同步事件。例如，`NSThread` 没有类似 Java 的 `join()` 方法，但使用 `NSConditionLock` 对象可以轻松实现这一功能，如清单 15-8 所示。  

### 清单 15-8. 执行 `join()` 操作  

**Java**  

```java  
public class Party implements Runnable {  

    public static void main(String[] args) {  
        Party party = new Party();  
        Thread thread = new Thread(party);  

        try {  
            System.out.println("Party starting");  
            thread.start();  
            thread.join();  
            System.out.println("Party is over");  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  

    public void run() {  
        System.out.println("partying...");  
    }  
}  
```  

**Objective-C**  

```objc  
@interface Party : NSObject {  
    NSConditionLock *joinLock;  
}  

[www.it-ebooks.info](http://www.it-ebooks.info/)  

第 15 章 ■ 线程  

+ (void)main;  
- (id)init;  
- (void)party:(id)ignored;  

@end  

@implementation Party  

+ (void)main  
{  
    Party *party = [Party new];  
    NSLog(@"Party starting");  
    [NSThread detachNewThreadSelector:@selector(party:)  
                             toTarget:party  
                           withObject:nil];  
    [party->joinLock lockWhenCondition:YES];  
    [party->joinLock unlock];  
    NSLog(@"Party is over");  
}  

- (id) init  
{  
    self = [super init];  
    if (self != nil) {  
        joinLock = [[NSConditionLock alloc] initWithCondition:NO];  
    }  
    return self;  
}  

- (void)party:(id)ignored  
{  
    NSLog(@"partying...");  
    [joinLock lock];  
    [joinLock unlockWithCondition:YES];  
}  

@end  
```  

约定的条件是一个 `BOOL` 值。`NSConditionLock` 初始创建时条件为 `NO`。主线程启动 `Party` 线程，然后尝试获取 `joinLock` 对象上的锁，但仅当其条件为 `YES` 时才获取。主线程会阻塞，直到互斥信号量既可用又处于所需状态。这只有在 `-party` 方法结束后才会发生，该方法最后会获取锁，并以新条件释放锁。此时锁空闲且条件满足，因此主线程中的锁可以被获取。  

你可以有条件地（`-lockWhenCondition:`）或无条件地（`-lock`）获取锁。随时可以使用 `-condition` 检查条件状态——但要注意，如果你没有拥有对象上的锁，条件随时可能改变。你只能在通过发送 `-unlockWithCondition:` 解锁互斥锁时更改条件。  

`NSConditionLock` 是一种在线程间协调状态的有效方式。清单 15-9 中的虚构示例展示了一个管理后台线程的对象。后台线程生成单个结果后暂停。主线程收集结果。一旦完成，后台线程立即回去工作，生成下一个结果。  

### 清单 15-9. 使用 `NSConditionLock` 进行线程状态管理  

```objc  
enum {  
    BusyBeeStateNone,  
    BusyBeeStateIdle,  
    BusyBeeStateBuzzing  
};  

@interface BusyBee : NSObject {  
```



```objc
NSConditionLock *state;

BOOL stopGathering;

id pollen;

}

- (id)init;

// 在主线程发送

- (id)collectPollen;

- (void)start;

- (void)stop;

// 后台线程

- (void)gatherPollenThread:(id)ignored;

@end

@implementation BusyBee

- (id) init

{

self = [super init];

if (self != nil) {

state = [[NSConditionLock alloc] initWithCondition:BusyBeeStateNone];

}

return self;

}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

```objc
- (id)collectPollen

{

[self start];

// 收集一个花粉对象

[state lockWhenCondition:BusyBeeStateIdle];

id returnPollen = pollen;

pollen = nil;

// 启动线程寻找更多花粉

[state unlockWithCondition:BusyBeeStateBuzzing];

return returnPollen;

}

- (void)start

{

[state lock];

NSInteger condition = [state condition];

if (condition==BusyBeeStateNone) {

[NSThread detachNewThreadSelector:@selector(gatherPollenThread:) toTarget:self

withObject:nil];

condition = BusyBeeStateBuzzing;

}

[state unlockWithCondition:condition];

}

- (void)stop

{

[state lock];

NSInteger condition = [state condition];

if (condition!=BusyBeeStateNone) {

stopGathering = YES;

if (condition==BusyBeeStateIdle)

condition = BusyBeeStateBuzzing; // 唤醒以停止

// 等待线程停止，即 join()

[state unlockWithCondition:condition];

[state lockWhenCondition:BusyBeeStateNone];

stopGathering = NO; // 为下次启动重置

}

[state unlock];

}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

```objc
- (void)gatherPollenThread:(id)ignored

{

[state lock];

while (!stopGathering) {

id foundPollen = pollen;

[state unlock];

if (foundPollen==nil) {

// 寻找花粉...

foundPollen = …

}

[state lock];

// 使花粉可用，转换为空闲状态

pollen = foundPollen;

if (stopGathering) // 可能在搜索过程中被设置

break;

[state unlockWithCondition:BusyBeeStateIdle];

// 等待重新开始收集

[state lockWhenCondition:BusyBeeStateBuzzing];

}

[state unlockWithCondition:BusyBeeStateNone];

}

@end
```

`NSConditionLock`对象提供了互斥信号量，使这段代码线程安全，同时也用于通信和控制`-gatherPollenThread`的状态。

从`BusyBeeStateNone`到`BusyBeeStateBuzzing`的初始状态转换由`-start`执行。

线程运行后，每当状态变为`BusyBeeStateBuzzing`时开始工作，并在任务完成后切换回`BusyBeeStateIdle`。

#### NSDistributedLock

`NSDistributedLock`是一种特殊的互斥信号量，它使用文件系统对象来协调进程间的操作，甚至可以通过共享文件系统协调其他计算机系统。它不遵循`NSLocking`协议，也没有`-lock`方法。通过指定可写文件系统上的文件路径进行初始化。`-tryLock`方法会测试对象并尝试获取锁。如果失败，代码必须在合适的间隔后重试。

清单 15-10 展示了使用`NSDistributedLock`防止备份期间修改一组数据库文件。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

**清单 15-10. 使用分布式锁**

```objc
NSString *dbPath = …

NSString *lockPath = [dbPath stringByAppendingPathComponent:@".dblock"]; NSFileManager *fileManager = [NSFileManager defaultManager];

// 锁定数据库目录

**NSDistributedLock *lock = [NSDistributedLock lockWithPath:lockPath];** **while (![lock tryLock])**

**[NSThread sleepForTimeInterval:1.0];**

// 将所有 .index 文件复制为 .backup

NSArray *dbFiles = [fileManager contentsOfDirectoryAtPath:dbPath error:NULL];

for ( NSString *file in dbFiles ) {

if ([[file pathExtension] isEqualToString:@"index"]) {

NSString *srcPath = [dbPath stringByAppendingPathComponent:file]; NSString *dstPath = [[srcPath stringByDeletingPathExtension]

stringByAppendingPathExtension:@"backup"];

[fileManager removeItemAtPath:dstPath

error:NULL];

[fileManager copyItemAtPath:srcPath

toPath:dstPath

error:NULL];

}

}

**[lock unlock];**
```

#### 自旋锁



```markdown
`@synchronized`指令和`NSLock`类族都易于使用且高效，但速度并非它们的优势。操作系统为高性能线程同步提供了一种非常特殊的互斥信号量，称为自旋锁。到目前为止讨论的所有互斥信号量在无法获取锁时都会挂起线程。挂起、切换和恢复线程是一项代价高昂的操作。如果一个频繁使用的方法为了完成一些琐碎任务而获取和释放锁，那么信号量以及任何相关线程切换的开销可能会成为显著的性能瓶颈。

当无法获取自旋锁时（因为其他线程已锁定它），该线程会“自旋”——持续轮询信号量的状态，直到它被解锁。线程不会被挂起，而是继续全速运行，以消耗 CPU 资源为代价，并抢占其他线程。如果这听起来像是可怕的资源浪费，那确实如此。自旋锁的优点在于，获取和释放一个未被竞争的锁所需的时间，与高级互斥信号量相比微不足道。因此，正常的、无竞争的情况运行得更快，性能提升最终会超过偶尔竞争导致的性能损失。

自旋锁在以下情况下有效：

*   两个线程同时尝试获取同一信号量的可能性非常小。
*   锁的获取和释放频率很高。
*   获取锁和释放锁之间的时间总是非常短——少于几百纳秒。

操作系统在竞争概率低、阻塞时间最小且性能至关重要的地方使用自旋锁。例如，操作系统的底层内存分配例程都使用自旋锁来协调来自多个线程的内存请求。

列表 15-11 再次重写了`FIFO`类——这次使用自旋锁而不是信号量对象。要使用自旋锁，您的代码必须首先`#include <libkern/OSAtomic.h>`。

**列表 15-11. 快速 FIFO 类**

```
#include <libkern/OSAtomic.h>

#import <Cocoa/Cocoa.h>

@interface FastFIFO : NSObject {
    NSMutableArray *stack;
    OSSpinLock spinLock;
}
@end

@implementation FastFIFO

- (id) init {
    self = [super init];
    if (self != nil) {
        stack = [NSMutableArray new];
    }
    return self;
}

- (void)push:(id)object {
    OSSpinLockLock(&spinLock);
    [stack addObject:object];
    OSSpinLockUnlock(&spinLock);
}

- (id)pop {
    id object = nil;
    OSSpinLockLock(&spinLock);
    if ([stack count]!=0) {
        object = [stack objectAtIndex:0];
        [stack removeObjectAtIndex:0];
    }
    OSSpinLockUnlock(&spinLock);
    return object;
}

- (BOOL)hasObjects {
    OSSpinLockLock(&spinLock);
    BOOL answer = ([stack count]!=0);
    OSSpinLockUnlock(&spinLock);
    return answer;
}

@end
```

`OSSpinLock`结构是不透明的，但在使用前应初始化为零。函数`OSSpinLockLock()`、`OSSpinLockUnlock()`和`OSSpinLockTry()`分别执行与`-[NSLock lock]`、`-[NSLock unlock]`和`-[NSLock tryLock]`相同的功能。每个自旋锁函数都传递自旋锁信号量结构的地址。

##### 操作

从列表 15-9 中的`BusyBee`示例可以看出，管理即使是“简单的”后台工作线程也不是一项轻松的任务。Mac OS X 10.5 和 Java 5.0 都认识到了这一点，并添加了类来为您管理自主后台进程。

Java 5.0 添加了一组令人眼花缭乱的新类和新接口来管理任务。主要的接口是`ExecutorService`，它有多个实现。执行器服务管理一组将在未来某个时间执行的任务。具体的子类，如`ThreadPoolExecutor`，会异步地在单独的线程中执行这些任务。
```


在 Objective-C 中，情况要简单得多。`NSOperationQueue`类管理一组`NSOperation`对象。每个`NSOperation`对象定义一个要执行的任务。`NSOperation`对象具有状态属性，指示它何时已启动、正在执行或已完成。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

使用`NSOperationQueue`可以非常简单，也可以相当复杂，具体取决于你的需求。使用`NSOperationQueue`的最小步骤如下：

1. 创建一个`NSOperationQueue`实例。
2. 创建一个`NSInvocationOperation`（`NSOperation`的一个具体子类）实例，指定要调用的对象和方法。或者，创建`NSOperation`的子类并重写其`-main`方法。
3. 向`NSOperationQueue`发送`-addOperation:`消息，并传入步骤 2 中创建的操作对象。

如果不做其他任何事情，`NSOperationQueue`将创建新线程并使用它们来执行`NSOperation`对象中的代码。第 4 章中使用的示例项目演示了`NSOperationQueue`使用起来有多么简单。使用`NSOperationQueue`有几个优点：

- `NSOperationQueue`会自动维护一个高效的线程池，为你创建和销毁线程。
- `NSOperationQueue`会根据系统资源（如 CPU 核心数）自动扩展，并执行负载均衡。
- 操作可以依赖于其他操作，从而允许你定义必须按正确顺序执行的复杂操作树。
- 你可以限制并发操作的数量，或者让操作队列自动确定一个最佳数量。
- 操作可以设置优先级。
- 通过子类化`NSOperation`，你可以对操作的行为进行广泛的控制。你还可以实现允许操作被提前取消的代码。

更多详细信息，请参阅《线程编程指南》中的“创建和管理操作对象”部分。²

### 定时器

定时器是在特定时间间隔内向运行循环发送方法调用消息的对象。它们使用起来非常简单，并且通常可以取代基于线程或其他机制的更复杂的解决方案。

定时器在运行循环上运行。它们有两种形式：重复定时器和一次性（非重复）定时器。重复定时器以固定间隔触发一个 Objective-C 消息，直到停止。一次性定时器发送一次消息，然后使自身失效。当定时器失效时，它将停止发送消息并从运行循环中移除自身。

要使用定时器：

1. 创建一个定时器对象。
2. 将其调度到当前运行循环上运行。
3. 要停止定时器，向其发送`-invalidate`消息。

² Apple Inc., 《线程编程指南》, 管理操作对象, http://developer.apple.com/documentation/Cocoa/Conceptual/Multithreading/OperationObjects/, 2008

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

创建定时器对象时，你需要指定时间间隔（以秒为单位）、一个可选的上下文对象，以及一个决定定时器是否重复的标志。定时器的目标要么是一个`NSInvocation`对象，要么是一个接收对象指针和方法标识符对。该方法必须期望接收`NSTimer`对象指针作为其唯一参数，并返回`void`。接收者可以使用定时器对象的`userInfo`属性来检索补充的上下文对象。

`NSTimer`对象可以使用任何`+timerWithTimeInterval:…`消息创建，然后稍后通过`-[NSRunLoop addTimer:forMode:]`调度到运行循环上运行。然而，更简单的方法是使用单个`+scheduledTimerWithTimeInterval:…`消息来创建并调度定时器运行。

清单 15-2 使用了一个单独的线程，大约每半秒在主线程上调用一次心跳消息。清单 15-12 提供了一个等效且更节约资源的解决方案，即使用定时器。

### 清单 15-12. 心跳定时器

```objc
@interface Process : NSObject

@property double progress;

@end

@interface Heartbeat : NSObject {
    NSTimer *timer;
    Process *monitor;
```



`NSProgressIndicator *indicator;`

}

`+ (Heartbeat*)startHeartbeatProcess:(id)process`

`withIndicator:(NSProgressIndicator*)progress;`

`- (void)stop;`

`- (void)heartbeatTime:(NSTimer*)timer;`

`@end`

`@implementation Heartbeat`

`+ (Heartbeat*)startHeartbeatProcess:(id)process`

`withIndicator:(NSProgressIndicator*)progress`

{

`Heartbeat *heartbeat = [Heartbeat new];`

`heartbeat->monitor = process;`

`heartbeat->indicator = progress;`

`heartbeat->timer = [NSTimer scheduledTimerWithTimeInterval:0.5 target:heartbeat selector:@selector(heartbeatTime:) userInfo:nil repeats:YES];`

return `heartbeat`;

}

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 15 章 ■ 线程

`- (void)stop`

{

`[timer invalidate];`

}

`- (void)heartbeatTime:(NSTimer*)timer`

{

`[indicator setDoubleValue:monitor.progress];`

}

`@end`

计时器并非极端精确，其精度会随着时间间隔的增加而降低。计时器可能早于或晚于预定时间触发，这取决于多种因素。由于计时器本质上是延迟消息，因此它们天生是线程安全的。

### 总结

基本的线程创建与同步与 Java 非常相似。你可以创建线程，并使用`@synchronized`指令控制它们对关键代码的访问。如果需要对线程同步进行更精细的控制，Objective-C 框架提供了多种互斥信号量，每种都有其独特的功能。

与 Java 类似，现代 Objective-C 框架现在提供了实用类，用于简化在多线程中创建和控制操作这一复杂任务。最后，别忘了极其有用的`NSTimer`类。那些需要在将来某个时间或以固定间隔执行的简单任务，可以轻松地通过计时器来调度。

[www.it-ebooks.info](http://www.it-ebooks.info/)

