# 第 18 章 ■ 提供者/订阅者模式

• Java 中的`Thermometer`类必须自己维护一组监听者对象。而 Objective-C 中`Thermometer`对象的观察者则由其`NSNotificationCenter`来管理。
• Java 解决方案通过`TemperatureListener`接口定义了将要调用的方法，并且监听者对象必须实现该接口。在 Objective-C 中，通知总是以`NSNotification`对象的形式传递。
• 发送者定义接收通知的 Java 方法。而在 Objective-C 中，由观察者决定它将接收的通知消息。

Objective-C 对象可以使用灵活的条件注册来观察通知。除了代码中展示的简单情况外，观察者可以选择从单个对象接收多条通知，或者从多个对象接收相似的通知。事实上，观察者甚至可以不需要持有提供者的引用，甚至不需要知道它是什么类。在分布式通知的情况下，提供者对象甚至可能不在同一个进程中。

`NSNotificationCenter`对象负责组织和管理观察者，并动态地将通知与观察者配对。`NSNotificationCenter`免去了提供者自行实现观察者管理的麻烦；提供者只需关注发送通知即可。由于`NSNotificationCenter`是通知的核心，接下来将讨论它们，然后介绍通知如何发布以及观察者如何注册以接收通知。

#### 通知中心

一个`NSNotificationCenter`管理着一组观察者。提供者可以向通知中心发布一条通知，通知中心会将其分发给所有在该中心注册的感兴趣观察者。

通常情况下，你的对象会与 Cocoa 框架为当前进程创建的单个默认`NSNotificationCenter`进行交互。这个每个进程唯一的实例通过类消息`[NSNotificationCenter defaultCenter]`获取。这里是通知的中央车站，也是 Cocoa 框架大部分功能所使用的通知中心。因此，如果你打算接收任何这些通知，你必须向`[NSNotificationCenter defaultCenter]`注册你的对象。

还有一些其他专用的通知中心，你也可以创建自己的通知中心。每个通知中心都是完全独立的。发布到一个通知中心的通知只会被传递给注册到该通知中心的对象。`NSWorkspace`对象定义了自己的通知中心，可以通过`[[NSWorkspace sharedWorkspace] notificationCenter]`获取。如果你想接收`NSWorkspace`通知，你的对象必须向该通知中心注册。

> **注意：** `NSNotificationCenter`会同步分发所有通知。但是，你可以将一个`NSNotificationQueue`（如本章后面“通知排队”部分所述）附加到通知中心，以实现异步分发通知。

你或许也想定义自己的通知中心。第 15 章展示了创建每线程通知中心的代码。由于通知总是在发布它们的线程中传递，每线程通知中心可以让你按线程来组织你的观察者。

#### 发布同步通知

发布同步通知很简单，列表 18-1 中的`Thermometer`类巧妙地演示了这一点。一条通知由三部分信息组成：

• 通知名称
• 发送者
• 一个包含补充信息的字典

通知名称是一个字符串对象。你可以选择任何值，只要不与其它对象的通知名称冲突即可。通常，像`@"TemperatureDidChange"`这样的描述性标题就足以保证唯一性。由框架定义的通知名称常量倾向于使用类命名约定。例如，所有 Cocoa 框架的通知名称都以“NS”开头。

通知中的对象属性包含对发送者的引用。因此，每条通知都隐式地包含一个指向发送它的对象的引用。在列表 18-1 和 18-2 的例子中，严格来说，并非必须将温度值包含在`userInfo`字典中。通知接收者可以通过`((Thermometer*)[notification object]).temperature`来获取该值。

可选的`userInfo`字典是一个不可变字典，包含提供者想包含的任何补充对象。如前所述，严格来说并非必须将温度值包含在`userInfo`字典中，但这通常是个好主意。将温度包含在字典中，免除了观察者对发送者做出过多假设（例如，“当收到通知时，新的温度值是否仍然有效？”）。

`userInfo`字典是包含其他有用或临时信息的绝佳场所，例如之前的温度值、温度变化速率等等。当然，在许多通知中，唯一有用的信息就是发送者本身。

通知名称和发送者是必需的。`userInfo`字典是可选的，也可以为空。有三种发布通知的方法：

- `- (void)postNotificationName:(NSString*)name object:(id)sender`
- `- (void)postNotificationName:(NSString*)name object:(id)sender userInfo:(NSDictionary*)info`
- `- (void)postNotification:(NSNotification*)notification`

前两条消息从名称、发送者和可选的字典构建一个新的`NSNotification`对象。最后一条消息接受一个预先组装好的`NSNotification`对象并发布它。请注意，虽然`object`参数是必需的，但并没有规定它必须是发送通知的对象。你可以代表其他对象发布通知。

在`-postNotification…`消息返回之前，通知会同步传递给同一线程中的所有观察者。也可以使用`NSNotificationQueue`（如本章后面“通知排队”部分所述）异步发送通知。

#### 做一个有辨别力的观察者

通知的真正力量在于观察者注册接收通知的灵活方式。要注册接收通知，需要向`NSNotificationCenter`发送一条`-addObserver:selector:name:object:`消息。这四个参数定义了它将接收哪些通知以及如何接收：

• `observer`和`selector`参数定义了将接收通知的对象以及将发送给它的 Objective-C 消息。该方法必须与`-(void)notificationMessage:(NSNotification*)notification`兼容。虽然`observer`参数通常是`self`，你也可以自由注册其他对象。
• `name`参数是所需通知的名称。
• `object`参数是所需的提供者。

`selector`参数允许观察者选择在通知发送时它将接收的消息。你的观察者可以将多条通知发送到同一个方法，或者将来自不同来源的同一通知路由到不同的方法。



