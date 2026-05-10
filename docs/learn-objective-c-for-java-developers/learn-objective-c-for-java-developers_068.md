# `@implementation DistributedNotificationListener`

```objc
- (void)dumpNotification:(NSNotification*)notification
{
    NSString* message = [notification name];
    id object = [notification object];
    NSDictionary* info = [notification userInfo];
    if (info != nil) {
        NSLog(@"%@ from %@ with %@", message, object, info);
    } else {
        if (object != nil)
            NSLog(@"%@ from %@", message, object);
        else
            NSLog(@"%@", message);
    }
}
@end
```

```objc
int main (int argc, const char * argv[])
{
    DistributedNotificationListener *listener;
    NSDistributedNotificationCenter *center;
 
    listener = [DistributedNotificationListener new];
    center = [NSDistributedNotificationCenter defaultCenter];
    [center addObserver:listener
              selector:@selector(dumpNotification:)
                  name:nil
                object:nil];
    [[NSRunLoop currentRunLoop] run];
    // 此调用永不返回：终止进程以停止程序
    return 0;
}
```

要使用分布式通知，你需要了解其一些限制和复杂性，这些将在接下来的几节中描述。

#### 分布式通知中心

要发布或观察分布式通知，你的对象必须与每个进程创建的单例 `NSDistributedNotificationCenter` 进行交互。你可以通过 `[NSDistributedNotificationCenter defaultCenter]` 获取它。未来框架可能会定义其他分布式通知中心，但目前只有一个。你不能创建自己的分布式通知中心。

如果需要更复杂的进程间通信路径，可以考虑使用分布式对象，详见第 13 章。

分布式通知中心使用 Mach（内核）端口交换消息。Mac OS X 的安全模型将 Mach 端口组织成引导命名空间的层次结构。发布的通知将广播到你账户的本地命名空间和父命名空间中的所有进程，但不会广播到其他用户创建的子命名空间。

#### 属性列表值

分布式通知中的信息是序列化的（参见第 12 章“Objective-C 序列化”部分）。这将 `object` 和 `userInfo` 参数中传递的对象限制为属性列表值——基本上是数组、字典、字符串、数字和日期对象。分布式通知中心中的 `object` 参数，其使用方式与普通通知中心完全相同，区别仅在于它只是一个字符串，而不是指向提供者对象的指针。使用描述性值（如 `@"com.apress.learnobjc.DemoProvider"`）来标识发送者或对通知进行分组。

#### 异步通知传递

与 `NSNotificationQueue` 类似，分布式通知会被排队、可选择合并，并异步传递。自 Mac OS X 10.3 起，分布式通知始终在主运行循环上传递。通知队列与分布式通知的主要区别在于，决定通知如何排队和合并的是观察者，而非提供者。

`NSDistributedNotificationCenter` 以两种模式之一运行：挂起或未挂起。未挂起时，通知会在发布时立即传递。挂起时，通知会根据观察者请求的 `suspensionBehavior:` 模式被丢弃、合并、缓冲或传递。四种可能的模式如表 18-4 所示。

**表 18-4.** *挂起通知中心观察者模式*

| 模式 | 描述 |
|------|------|
| `NSNotificationSuspensionBehaviorDrop` | 通知被丢弃。 |
| `NSNotificationSuspensionBehaviorCoalesce` | 相似的通知被合并并保存。此为默认模式。 |
| `NSNotificationSuspensionBehaviorHold` | 通知被保存，直到达到通知中心的缓冲区上限。 |
| `NSNotificationSuspensionBehaviorDeliverImmediately` | 忽略分布式通知中心的挂起状态，无论如何立即传递通知。 |




观察者使用 `-addObserver:selector:name:object:suspensionBehavior:` 消息请求挂起模式。常规的 `-addObserver:selector:name:object:` 消息会将观察者注册到 `NSNotificationSuspensionBehaviorCoalesce` 模式下。

提供者可以通过使用 `-postNotificationName:object:userInfo:deliverImmediately:` 消息并传递 `YES` 作为 `deliverImmediately:` 参数，强制通知立即投递。此操作会忽略挂起状态和行为模式，直接将通知投递给所有观察者。

**暂停分布式通知中心**

分布式通知中心可以通过 `-setSuspended:` 消息进行挂起或重新激活。当它被重新激活时，所有缓冲的通知会立即被投递。

应用工具包（GUI 框架）应用程序中的分布式通知中心会在应用程序变为非活跃状态时自动挂起——即当它不再是前台应用程序时。当应用程序重新变为活跃状态时，它会自动取消挂起。你不应干扰此行为；请使用 `NSNotificationSuspensionBehaviorDeliverImmediately` 或 `deliverImmediately:` 选项，将通知投递给处于挂起状态应用程序中的观察者。守护进程或命令行工具可以根据需要挂起或激活其分布式通知中心。

**总结**

通知为不同对象之间的通信提供了一种灵活的方式。观察者可以注册以接收特定通知或广泛的通知。观察者可以注册以接收来自它们一无所知的对象的通知。通知中心负责为你的对象管理提供者和观察者。

通知也可以被推送到队列中异步投递，或者发布到分布式通知中心以在进程间交换。通过合并冗余通知可以实现简单的负载均衡。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 19 章 — 观察者模式

观察者模式在源对象和一组观察者之间创建了一种一对多的单向关系。当源对象的某个属性发生变化时，观察者会收到一条通知消息。观察者可以利用这些信息与所观察的对象保持同步，或执行其他任务。

是我多心了，还是这听起来和上一章的提供者/订阅者模式完全一样？在这个抽象层面上，提供者/订阅者模式和观察者模式非常相似。

在 Java 中，两者都会像第 18 章所阐述的那样，通过监听器接口、对象和管理来实现。但观察者模式和提供者/订阅者模式是不同的，如表 19-1 所述。

**表 19-1.** 提供者/订阅者模式与观察者模式的区别

| 提供者/订阅者模式 | 观察者模式 |
| --- | --- |
| 提供者/订阅者模式以通知为中心。通知对象可以包含任意信息，并可以为任何目的而定义。 | 观察者模式关注对象的特定属性。观察者观察另一个对象的某个属性，并在该属性发生变化时得到通知。通知中包含的唯一信息是对象本身以及变化的详情。 |
| 提供者对象积极参与提供者/订阅者设计模式。它明确地定义它将发送哪些通知以及何时发送。 | 被观察的对象通常是*被动*的。它不定义哪些属性可以被观察，也不需要在属性变化时执行任何操作；框架会代表它生成通知。 |
| 提供者必须实现代码来发布通知。 | 被观察的对象无需实现任何代码即可被观察。如果某个类期望被观察，并且具有非平凡的属性或关系，你可以选择实现自定义通知代码。 |
| 通知被发布到通知中心。 | 被观察属性的通知直接从源对象发送到它的观察者。 |

Objective-C 提供了一种非常具体的技术来实现观察者模式，称为键值观察。这是一项需要理解的重要技术，因为它是渗透在用户界面框架中的模型-视图-控制器模式的一个组成部分。由于键值观察具有非常独特的能力，本章中没有 Java 示例。从本质上讲，它们会与第 18 章中的 Java 示例完全相同。

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 第 19 章 ■ 观察者模式

本章描述了观察者模式的基本原理以及键值观察技术。你至少需要阅读这些内容来理解 KVO。后续章节将解释如何使用 KVO，以及如何为非平凡属性添加 KVO 支持。键值观察使用键值编码。如果这听起来不熟悉，你可能需要回顾一下第 10 章的“键值编码”部分，快速复习一下。

### 键值观察的工作原理

键值观察的工作原理是：当另一个对象（被观察对象）的特定属性发生变化时，通知一个对象（观察者）。这种关系是通过向将被观察的对象发送单条 `-addObserver:…` 消息来建立的。一旦建立，每当被观察对象的那个属性发生变化时，观察者对象就会收到一条通知消息。清单 19-1 展示了一个键值观察的简单示例。首先是类，然后是执行的代码，最后是程序的输出。

**清单 19-1.** 键值观察示例

```objectivec
@interface Chameleon : NSObject {
    NSColor *color;
}
@property (assign) NSColor *color;
@end

@implementation Chameleon
- (NSColor*)color {
    return color;
}
- (void)setColor:(NSColor*)newColor {
    color = newColor;
}
@end

@interface Watcher : NSObject
@end

@implementation Watcher
- (void)observeValueForKeyPath:(NSString*)keyPath
                      ofObject:(id)object
                        change:(NSDictionary*)change
                       context:(void*)context {
    NSLog(@"对象 '%@' 的属性 '%@' 已变更: %@", keyPath, object, change);
}
@end

...

Chameleon *chameleon = [Chameleon new];
Watcher *watcher = [Watcher new];

[chameleon addObserver:watcher
            forKeyPath:@"color"
               options:NSKeyValueObservingOptionNew
               context:NULL];

chameleon.color = [NSColor greenColor];

输出:
对象 '<Chameleon: 0x139330>' 的属性 'color' 已变更: {
    kind = 1;
    new = NSCalibratedRGBColorSpace 0 1 0 1;
}
```

以下是清单 19-1 中发生的情况：

1. `Chameleon` 类实现了一个属性。这个属性没有什么特别之处，只是它符合键值编码的准则。
2. `Watcher` 类实现了一个 `-observeValueForKeyPath:ofObject:change:context:` 方法。这使其有资格成为键值观察者。
3. 观察者对象注册为 `Chameleon` 对象的 `color` 属性的观察者。
4. 当 `Chameleon` 对象的 `color` 属性发生变化时，观察者对象会收到一条通知，告知它发生变化的是哪个属性（键路径）以及新的值。

简而言之，这就是键值观察。任何实现了 `-observeValueForKeyPath:ofObject:change:context:` 方法的对象，都可以注册来观察另一个对象的几乎任何属性。每当该属性发生变化时，观察者就会收到一条通知。

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 键值观察的魔法

如果你正挠头思索键值观察框架是如何知道 `Chameleon` 对象收到了 `-setColor:` 消息的，那它是通过一种叫做 isa 交换的小魔法做到的。在第 26 章中会对此进行更详细的描述。



