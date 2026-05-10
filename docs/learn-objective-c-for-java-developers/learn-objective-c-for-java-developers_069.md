# KVO 工作原理

`KVO` 利用了 Objective-C 对象的动态特性。当观察者请求观察 `Chameleon` 对象时，`KVO` 框架会动态地为 `Chameleon` 创建一个新的 Objective-C 子类，重写其 `-setColor:` 方法，并将该对象的类更改为新的子类。当 `Chameleon` 对象随后收到 `-setColor:` 消息时，合成子类的方法会被调用。该子类方法先调用 `[super setColor:newColor]`，然后将请求的属性变化通知发送给所有观察者。

这一过程快速、高效，且对被观察对象完全透明。

`KVO` 仅重写被观察属性的 `setter` 方法，因此对其他方法没有性能影响。如果稍后移除某个对象的所有观察者，`KVO` 会将该对象的类恢复为原始类。

### 与提供者/订阅者及观察者模式的比较

延续此前对提供者/订阅者模式与观察者模式的比较，第 18 章中介绍的 `Thermometer` 和 `TemperatureMonitor` 示例本可以轻松使用键值观察（`Key-Value Observing`）实现，但存在以下注意事项：

- `Thermometer` 对象无法根据其发布通知的通知中心来选择观察者受众。
- 温度变化通知除了包含新值（以及可能的旧值）外，不会包含关于变化的任何额外信息。
- 观察者无法注册以接收来自未知对象的通知。
- 温度变化通知无法排队以进行异步投递或合并。
- 变化通知只能发送给同一进程内的对象。

### KVO 的核心特性

键值观察最重要的特性是，它通常不需要被观察对象提供任何编程支持。例如，`NSProgressIndicator` 类定义了一个符合 `KVC`（`Key-Value Coding`）规范的 `indeterminate` 属性。这是一个简单的只读 `BOOL` 属性值，要么设置，要么不设置。如果你有一个用户界面对象需要根据进度指示器的 `indeterminate` 状态改变外观，你的对象可以观察该视图对象的 `indeterminate` 属性，并在其发生变化时自行更新。漂亮之处在于，`NSProgressIndicator` 从未被设计为在其 `indeterminate` 属性变化时通知观察者，但借助 `KVO`，它仍然可以参与观察者设计模式。

> **注意**：键值观察也会注意到通过键值编码（`Key-Value Coding`）进行的属性更改，例如 `[chameleon setValue:[NSColor greenColor] forKey:@"color"]`，如第 10 章“键值编码”部分所述。然而，`KVO` 无法观察通过直接赋值进行的属性更改，例如 `chameleon->color=[NSColor greenColor]`。如果使用直接赋值并且希望你的类兼容键值观察，则需要实现手动 `KVO` 通知，这将在本章后面解释。

[www.it-ebooks.info](http://www.it-ebooks.info/)

