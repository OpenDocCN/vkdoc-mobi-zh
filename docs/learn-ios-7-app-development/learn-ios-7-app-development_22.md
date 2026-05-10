# 观察通知

观察通知的基本模式为：

创建一个方法来接收通知消息。成为您的对象感兴趣的特定通知的观察者。处理接收到的所有通知。在不再需要观察通知时，或在对象销毁前停止观察通知。

第一步非常简单。在您的 `MSMasterViewController.m` 实现文件中，添加一个 `-whatsitDidChangeNotification:` 方法。首先，在 `@interface MSMasterViewController ()` 部分的末尾添加一个方法原型：

`- (void)whatsitDidChangeNotification:(NSNotification*)notification;`

然后，在 `@implementation` 部分的底部附近，添加实际的方法体：

```
- (void)whatsitDidChangeNotification:(NSNotification*)notification
{
    NSUInteger index = [things indexOfObject:notification.object];
    if (index!=NSNotFound)
    {
        NSIndexPath *path = [NSIndexPath indexPathForItem:index inSection:0];
        [self.tableView reloadRowsAtIndexPaths:@[path]
                              withRowAnimation:UITableViewRowAnimationNone];
    }
}
```

所有通知消息都遵循相同的模式：`-(void)myNotification:(NSNotification*)theNotification`。您可以随意命名您的方法，但它必须接收一个单一的 `NSNotification` 对象作为其唯一参数。

`notification` 参数包含通知的所有详细信息。通常您并不关心这些细节，特别是当您的对象只想知道通知发生了，而不需要确切原因时。在这种情况下，您关心的是通知的 `object` 属性。每个通知都有一个 `name` 和一个与之关联的 `object`——通常正是这个 `object` 触发了通知。

该方法的第一行在您的 `things` 集合中查找通知的 `object`。如果该对象是集合中的 `MyWhatsit` 对象，`-indexOfObject:` 方法将返回其在集合中的索引。如果不是，它将返回常量 `NSNotFound`。

如果该对象在您的表格中（`index != NSNotFound`），那么接下来的两行代码将创建一个指向表格中该对象位置的 `NSIndexPath`，然后告诉表格视图重新加载（重新显示）那一行，无需动画效果。

最终结果是，每当详情视图更改一个 `MyWhatsit` 对象时，此方法都会导致表格中对应的行重新绘制，从而显示该更改。现在，您只需将 `MSMasterViewController` 注册到通知中心，以便它能接收到此消息。

找到 `-awakeFromNib` 方法。紧接在您添加的用于创建 `things` 测试数组的代码之后，添加以下语句：

```
[[NSNotificationCenter defaultCenter]
    addObserver:self
       selector:@selector(whatsitDidChangeNotification:)
           name:kWhatsitDidChangeNotification
         object:nil];
```

这条消息告诉通知中心，当发布任何对象（`nil`）且名称为 `kWhatsitDidChangeNotification` 的通知时，注册此对象（`self`）以接收指定的消息（`-whatsitDidChangeNotification:`）。

## 通知匹配

注册为通知观察者非常灵活。通过在 `-addObserver:selector:name:object:` 方法中为 `name` 或 `object` 参数传入 `nil` 常量，您可以请求接收具有指定名称的通知、来自特定对象的通知，或两者都指定。下表展示了成为观察者时 `name` 和 `object` 参数的效果。

| name: | object: | 接收的通知类型 |
| --- | --- | --- |
| `@"Name"` | `object` | 仅接收对象 `object` 发出的名为 `@"Name"` 的通知 |
| `@"Name"` | `nil` | 接收任何对象发出的所有名为 `@"Name"` 的通知 |
| `nil` | `object` | 接收对象 `object` 发出的每一个通知 |
| `nil` | `nil` | 接收每一个通知（不推荐） |

在这种场景下，您希望在任何一个 `MyWhatsit` 对象被编辑时收到通知。您的代码随后会检查具体的 `object` 以确定它是否相关。在其他情况下，您可能希望在特定对象发送特定通知时才接收通知，而忽略来自不相关对象的类似通知。

与注册接收通知同样重要的是，当您的对象不再需要接收通知时，要取消注册。对于这个应用来说，虽然没有任何时刻通知会变得不相关，但您仍然必须确保在对象销毁前它不再是观察者。在 iOS 中，将已销毁的对象注册为观察者是导致应用崩溃的常见原因。因此，请务必确保在对象消失之前将其从通知中心移除。

确保这一点非常容易，所以您没有借口不这样做。就在您的 `-awakeFromNib` 方法上方，添加以下方法：

```
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

`-dealloc` 消息会在您的对象即将销毁前发送给该对象。在此方法中，您应该清理所有无法自动处理的“遗留问题”。这条语句告诉通知中心，此对象不再是任何通知的观察者。您甚至不需要记住之前请求观察哪些通知或对象；这条消息将撤销所有这些注册。

再次在 iPhone 模拟器中运行您的应用。编辑一个项目，然后返回到列表。这次，您的更改会显示在列表中！

## 模态编辑与非模态编辑

您已进入冲刺阶段。事实上，您离终点线已近在咫尺，几乎可以触摸到它了。只有一个令人困扰的细节需要修复：iPad 界面。

iPhone 界面使用的是软件开发人员所说的模态界面：当您点击一行来编辑一个项目时，您会被带到一个可以编辑其详情（编辑模式）的屏幕，然后您退出该屏幕并返回列表（浏览模式）。

iPad 界面则不是这样工作的。尤其是在横屏模式下，您可以在主列表和详情视图之间随意跳转。这意味着您可以开始编辑标题或位置，然后立即切换到列表中的另一个项目。这被称为非模态界面。

虽然这能带来流畅的用户体验，但对您的应用来说却是一场灾难。如果您打开 `Main_iPad.storyboard` 文件，并将名称和位置文本字段的 `Editing Did End` 事件连接到 `-changedDetail:` 消息（就像您在 iPhone 界面中做的那样），您会发现它不起作用。

尽管试试看。我等着。

这是因为在您切换到列表中的另一个 `MyWhatsit` 对象之前，文本字段的编辑永远没有机会“结束”。幸运的是，有一个简单的解决办法。不要将 `Editing Did End` 事件连接到 `-changedDetail:`，而是连接 `Editing Changed` 事件。这个事件是一个“更低层级”的事件，每当用户在文本字段中进行任何更改时都会发送。这样，当用户编辑详情时，表格视图中的行将随之更新。

## 细节打磨

通过为您的应用添加图标来完善它，就像您在前一章为 EightBall 应用所做的那样。找到您在第一章下载的 `Learn iOS Development Projects` 文件夹。在 `Ch 5` 文件夹内，您会找到 `MyStuff (Icons)` 文件夹。在导航器中选择 `images.xcassets` 项，然后选择 `AppIcon` 图像组。将所有图像文件从 `MyStuff (Icons)` 文件夹拖放到该组中。Xcode 会自动对它们进行分类。

您的应用已完成，但我想花点时间向您介绍其他与表格相关的主题。



