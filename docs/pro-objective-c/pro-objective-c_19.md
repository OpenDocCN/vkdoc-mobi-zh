# 第 12 章：Foundation 框架专用服务

在本章中，您将探索几个提供专用系统服务的 Foundation 框架类。这些类实现了功能，以支持通过通知进行事件驱动编程、通过归档和序列化实现对象持久化，以及通过分布式对象实现分布式编程。

## 通知

Foundation 框架包含一组 API——*通知支持类*——它们为事件驱动编程提供了强大的机制。*通知*封装了关于某个事件的信息。它可以发送给一个或多个观察对象，以响应程序中发生的事件。通知架构遵循广播模型；因此，接收事件的对象与发送事件的对象是解耦的。通知支持类支持创建和发布通知、发送和接收通知，以及通过队列异步发布通知。这些 API 还支持注册通知以及将通知投递到特定线程。

`NSNotification` 封装了通知对象发送的信息。它由唯一的名称、发布对象以及（可选）一个补充信息字典组成。该类包含创建和初始化通知以及检索通知信息的方法。清单 12-1 为一个简单的问候语创建了一个名为 `ApplicationDidHandleGreetingNotification` 的通知。

*清单 12-1.* 使用 `NSHost` 检索网络主机名

```
NSString *nName = @"ApplicationDidHandleGreetingNotification";
NSNotification *notif = [NSNotification notificationWithName:nName
                                                      object:@"Hello, World!"];
```

那么您可能会想，为什么通知的名称这么长？嗯，Apple 提供了一些关于通知命名的指南。它建议通知名称应由以下元素组成：

- 关联类的名称
- 单词 *Did* 或 *Will*
- 名称的唯一部分
- 单词 *Notification*

这些指南旨在促进创建具有自描述性的唯一通知名称。



### NSNotificationCenter 和 NSDistributedNotificationCenter

`NSNotificationCenter`和`NSDistributedNotificationCenter`类提供了向感兴趣的观察者广播通知的机制。它们包含用于获取通知中心实例、添加和移除通知观察者以及发布通知的方法。`NSDistributedNotificationCenter`是`NSNotificationCenter`的子类，提供了向其他进程广播通知的机制。类方法`defaultCenter`用于检索与应用程序关联的默认通知中心实例，如下所示：

```
[NSNotificationCenter defaultCenter];
```

`NSNotificationCenter`提供了几种发布通知的方法。以下语句使用`postNotification:`方法发布在清单 12-1 中创建的通知：

```
[[NSNotificationCenter defaultCenter] postNotification:notif];
```

`NSNotificationCenter`还提供了通过单个方法创建和发布通知的方法。以下语句使用`postNotificationName:object:`方法，使用在清单 12-1 中创建的通知名称来创建和发布通知：

```
[[NSNotificationCenter defaultCenter] postNotificationName:nName
                                                    object:@"Salut!"];
```

`NSNotificationCenter`包含了几种管理通知观察者的方法。您可以使用`addObserver:selector:name:object:`方法注册一个对象以接收通知。此方法接受参数，指定接收通知的观察者对象、要发送给观察者的消息、要为其注册观察者的通知名称，以及观察者希望从中接收通知的发送者。清单 12-2 注册了一个名为`handler`的对象（`Greeter`类的实例），以从名为`nName`的变量（参见清单 12-1）接收通知，并将消息`handleGreeting:`发送给观察者。

*清单 12-2.*  注册通知观察者

```
Greeter *handler = [Greeter new];
[[NSNotificationCenter defaultCenter] addObserver:handler
                                        selector:@selector(handleGreeting:)
                                            name:nName
                                          object:nil];
```

接收通知事件的类必须实现具有以下签名的方法：

```
- (void) methodName :(NSNotification *)notification;
```

`methodName`是方法的名称。在清单 12-2 中，`Greeter`类实现了一个名为`handleGreeting:`的通知处理方法。

`NSNotificationCenter`还提供了移除观察者的方法；`removeObserver:`方法取消注册观察者，使其不再接收之前注册的通知。`removeObserver:name:object:`方法则更精细，它移除与指定观察者、通知名称和发送者匹配的条目。以下语句为名为`handler`的观察者移除了变量名为`nName`的通知：

```
[[NSNotificationCenter defaultCenter] removeObserver:handler
                                                name:uName
                                              object:nil];
```

### NSNotificationQueue

`NSNotificationQueue`类为通知中心提供了一个队列，从而支持异步发布和/或合并通知。*合并*是一个过程，使您能够（从队列中）过滤与先前排队的通知在某种程度上相似的通知。该类包括获取通知队列实例以及在队列中添加/移除通知的方法。类方法`defaultQueue`用于检索当前线程的默认通知队列实例，如下所示：

```
[NSNotificationQueue defaultQueue];
```

方法`initWithNotificationCenter:`提供了为指定通知中心创建通知队列的能力。清单 12-3 检索默认通知中心，然后创建一个与其关联的通知队列。

*清单 12-3.*  为指定通知中心创建通知队列

```
NSNotificationCenter *notifier = [NSNotificationCenter defaultCenter];
NSNotificationQueue *queue = [[NSNotificationQueue alloc]
                              initWithNotificationCenter:notifier];
```

`NSNotificationQueue`包含两种发布通知的方法。`enqueueNotification:postingStyle:coalesceMask:forModes:`方法使您能够指定发布通知的发布风格、合并选项以及支持的运行模式。合并选项是常量值，定义如下：

*   `NSNotificationNoCoalescing`：不合并通知，发布所有通知。
*   `NSNotificationCoalescingOnName`：合并具有相同名称的通知，以便只发布一个。
*   `NSNotificationCoalescingOnSender`：合并具有相同发送者的通知，以便只发布一个。

发布风格定义了用于排队通知的交互模式（同步/异步、空闲时/尽快）。这些选项指定为常量值，如下所示：

*   `NSPostASAP`：在相应运行循环的当前迭代完成后，尽快异步发布。
*   `NSPostWhenIdle`：在运行循环等待输入源或定时器事件时异步发布。
*   `NSPostNow`：排队的通知（在合并后）立即发布，有效提供同步行为。此行为不需要运行循环。

清单 12-4 同步排队分配给变量`notif`的通知（参见清单 12-1），合并具有相同名称的通知。

*清单 12-4.*  使用合并将通知发布到队列

```
[[NSNotificationQueue defaultQueue]
    enqueueNotification:notif
           postingStyle:NSPostNow
           coalesceMask:NSNotificationCoalescingOnName
               forModes:nil];
```

`NSNotificationQueue`还包含一个从队列中移除通知的方法。以下语句使用`dequeueNotificationsMatching:coalesceMask:`方法，在不进行合并的情况下，移除去队列分配给变量`notif`的通知。

```
[[NSNotificationQueue defaultQueue]
     dequeueNotificationsMatching:notif
                     coalesceMask:NSNotificationNoCoalescing];
```

## 归档和序列化



