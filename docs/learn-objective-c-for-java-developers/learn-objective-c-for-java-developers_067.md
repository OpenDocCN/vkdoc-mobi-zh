# `name` 和 `object` 参数可以有一个或同时为 `nil`。省略时，它们充当通配符，接受与其它条件匹配的任何通知。表 18-1 列出了观察者可以指定的 `name` 和 `object` 值的组合。

**表 18-1.** 观察者通知条件

| `name`    | `object`   | 通知条件                                                       |
|-----------|------------|----------------------------------------------------------------|
| `@"name"` | `object`   | 接收由 `object` 发送的、名称为 `@"name"` 的通知。               |
| `@"name"` | `nil`      | 接收来自任何 `object` 的、名称为 `@"name"` 的所有通知。         |
| `nil`     | `object`   | 接收由 `object` 发送的所有通知。                                |
| `nil`     | `nil`      | 接收发送到此通知中心的所有通知。                                |

第二种形式最为常见。观察者将接收由任何对象发送的、指定名称的所有通知。观察者无需知道该对象是什么，也无需持有该对象的引用来进行注册。它只需知道通知的名称。

以下是两个实际示例，均使用通知来管理辅助窗口。

- 您的应用程序实现了一个持久的“检查器”面板窗口，它始终显示最前文档窗口的信息。面板窗口对象将注册以接收来自任何对象（`nil`）的 `NSWindowDidBecomeMainNotification` 通知。每当某个文档窗口变为活动窗口时，它会收到通知并更新其显示内容。变为活动的窗口包含在通知中。

- 您的应用程序实现了一个附加到特定窗口的“检查器”面板窗口。面板窗口对象将注册以接收来自特定文档窗口对象的 `NSWindowDidBecomeMainNotification` 和 `NSWindowDidResignMainNotification` 通知。每当该特定窗口变为活动或非活动状态时，面板都会收到通知。面板窗口利用这些事件自动显示或隐藏自身。

观察者可以针对多个通知名称、不同的提供者或两者的某种组合，多次注册以接收通知。如果观察者以不同方式注册了同一个通知，它可能会多次收到同一个通知。其顺序是不可预测的。

#### 移除观察者

观察者可以通过向通知中心发送 `-removeObserver:` 或 `-removeObserver:name:object:` 消息来取消先前的注册。`observer` 参数是必需的，而 `name` 和 `object` 参数都是可选的，且都可以是 `nil`。通知中心会撤销所有与条件匹配的通知的订阅，如表 18-2 所列。

**表 18-2.** 移除观察者条件

| `name`    | `object` | 注册效果                                                              |
|-----------|----------|------------------------------------------------------------------------|
| `nil`     | `nil`    | 取消该观察者的所有注册。与 `-removeObserver:` 相同。                     |
| `@"name"` | `nil`    | 取消所有名称为 `@"name"` 的通知的注册。                                   |
| `nil`     | `object` | 取消所有由 `object` 发送的通知的注册。                                    |
| `@"name"` | `object` | 取消由 `object` 发送的、名称为 `@"name"` 的通知的注册。                   |

在上面的第二个面板窗口示例中，观察者已注册接收两个特定通知，且均来自同一特定提供者。消息 `[notificationCenter -removeObserver:self name:nil object:paletteWindow]` 会移除这两个注册，因为两者都指定了相同的提供者。但是，消息 `[notificationCenter -removeObserver:self name:NSWindowDidBecomeMainNotification object:nil]` 只会移除一个注册。

在垃圾回收内存环境中，通知中心对观察者持有弱引用。一旦您的观察者被遗忘并被回收，它实际上就会从其通知中心移除。因此，在您的对象可以被回收之前，并不需要发送 `[[NSNotificationCenter defaultCenter] removeObserver:self]`。但是，如果存在一个明显的时机，您的对象应该在此后停止接收通知，那么将其从通知中心移除是恰当且可取的。

### 通知队列



## 排版后

`NSNotificationQueue`对象管理一个尚未投递的通知队列。`NSNotificationQueue`对象（或简称通知队列）允许你将同步的通知中心转变为异步的通知服务。通知被投递到通知队列中，该队列将在未来的某个时间将它们传递给其通知中心进行分发。

每个通知队列都附属于一个现有的通知中心。它不会改变通知中心的行为。通知队列将通知放入缓冲区，然后使用当前的运行循环（run loop）稍后将其出队并投递到其通知中心。每个线程都有一个默认的通知队列，你也可以根据需要创建额外的队列。你可以创建多个共享同一个通知中心的队列；请记住，从通知中心的角度来看，队列只是另一个通知来源。

通知队列具有几个有用的特性：

- 队列中的通知可以以不同程度的紧急程度进行投递。
- 相似的通知可以被合并（coalesced），以便在生成多个通知但尚未投递时，观察者只收到一个通知。
- 可以将未投递的通知从队列中移除。

以下章节描述了从队列中排队、合并和移除通知的过程。

■ 注意事项 通知队列需要正在运行的运行循环才能正常工作。如果你的线程未使用运行循环，则无法使用该线程的通知队列。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 18 章 ■ 提供者/订阅者模式**

#### 排队一个通知

排队一个通知与直接将通知投递到通知中心并没有太大区别。首先，你必须为队列提供一个`NSNotification`对象——没有便捷方法可以为你创建一个。其次，你必须指定其紧急程度，称为其投递样式（posting style），并且可选地指定它应如何与队列中已有的相似通知进行合并。合并将在下一节中描述。

要将*清单 18-1*中的`Thermometer`类改为使用通知队列而不是通知中心，请将`-setTemperature:`方法替换为*清单 18-3*中的代码。

**清单 18-3. 排队的温度通知**

```
- (void)setTemperature:(float)newTemp

{

if (temperature!=newTemp) {

temperature = newTemp;

NSNumber *tempValue = [NSNumber numberWithFloat:temperature]; NSDictionary *info = [NSDictionary dictionaryWithObject:tempValue forKey:@"Temperature"];

NSNotification *n = [NSNotification notificationWithName:TempDidChange object:self

userInfo:info];

[[NSNotificationQueue defaultQueue] enqueueNotification:n

postingStyle:NSPostASAP];

}

}
```

一个通知对象被创建并放入队列中。它将在下一次机会时被运行循环从队列中取出，并投递到默认的通知中心。有三种可能的投递样式，列于*表 18-3*中。

**表 18-3. 通知队列投递样式**

| 样式 | 描述 |
|------|------|
| `NSPostASAP` | 通知将在下一次机会时由运行循环投递，与其他常规运行循环事件一起处理。这是典型的投递样式。 |
| `NSPostWhenIdle` | 通知将留在队列中，直到运行循环空闲。也就是说，运行循环没有其他等待立即分发的事件。 |
| `NSPostNow` | 同步投递通知。这与直接将通知投递到通知中心相同，不同之处在于队列可能首先执行通知合并。 |

现在，`Thermometer`类将通知排队，以便在未来的某个时间点异步投递。在异步设计中，提供者更需要在通知的`userInfo`字典中包含相关信息（例如当前温度值），这一点甚至更为重要。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 18 章 ■ 提供者/订阅者模式**



当通知到达时，`Thermometer` 的状态可能与其发送通知时的状态不同。

以相同投递样式推送的多个通知将按照入队顺序依次传递。

#### 通知合并

当你将通知加入队列时，可以选择性地指定一个合并掩码和一个运行循环模式。合并掩码决定该通知如何与队列中已有的类似通知进行匹配。匹配标准与请求通知时使用的相同：通知必须与当前队列中任意通知的名称、发送者或两者兼匹配。匹配条件是通过对 `NSNotificationCoalescingOnName` 或 `NSNotificationCoalescingOnSender` 中的一个或两个进行逻辑或运算形成的。如果该通知与队列中的其他通知匹配，则这些通知会被移除，并由新通知替换。同样，我们通过将 `-enqueueNotification:postingStyle:` 消息替换为代码清单 18-4 中的代码来修改 `Thermometer` 类。

**代码清单 18-4.** 合并后的温度通知

```
[[NSNotificationQueue defaultQueue] enqueueNotification:n
                                  postingStyle:NSPostWhenIdle
                                  coalesceMask:(NSNotificationCoalescingOnName|NSNotificationCoalescingOnSender)
                                      forModes:nil];
```

现在，温度变化事件会被推入队列，只有当运行循环空闲时才会传递给它们的观察者。如果在第一个通知传递之前，来自同一 `Thermometer` 对象的第二个或第三个温度变化通知被发出，则较早的那些通知会被丢弃。最终，只有最后一个发出的通知会被发送给观察者。

`forModes:` 参数让你可以指定通知传递时的运行循环模式。通常这是 `nil` 或 `NSDefaultRunLoopMode`。如果你希望通知在特定模式下传递（例如，仅在运行循环处理模态对话框事件时），可以使用此参数。有关运行循环模式的更多信息，请参阅第 15 章的“运行循环”部分。如果你想指定运行循环模式但不进行合并，可以传递常量 `NSNotificationNoCoalescing`。

#### 通知出列

如果你后来改变了对某个通知的主意，并且它尚未被传递，则可以从队列中删除它。向通知队列发送一条带有你要移除的通知以及常量 `NSNotificationNoCoalescing` 的 `-dequeueNotificationsMatching:coalesceMask:` 消息即可。如果改为传递“通知合并”部分中描述的合并掩码组合，则所有会与该通知合并的其他通知也将被移除。

### 分布式通知

分布式通知能够向其他进程中的对象发送通知。它们与常规通知非常相似，但有三个重要区别：

- 通知会发送给所有进程。
- 通知只能包含属性列表值。
- 通知以异步方式发布和接收。

第一个特点正是分布式通知如此强大的原因。只需一条 `-postNotification…` 消息，就能在不同进程中的对象之间进行通信，这使得分布式通知成为迄今为止最易用的进程间通信服务。虽然对象可以位于不同进程中，但并非必须如此；分布式通知也会传递给所有本地观察者。

代码清单 18-5 中的程序可能很有趣。它会注册以接收所有分布式通知并将其记录下来。将其构建为命令行工具，运行它，然后正常使用你的系统。或者用它来测试从你的应用程序发送通知。终止该进程即可停止它。

**代码清单 18-5.** 分布式通知监视器

```
#import <Foundation/Foundation.h>
#import <AppKit/NSWorkspace.h>

@interface DistributedNotificationListener : NSObject
- (void)dumpNotification:(NSNotification*)notification;
@end
```



