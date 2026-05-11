# 第 4 章：在应用中保存内容

```objective-c
- (void)prepareForReuse
{
    [self setPossession:nil];
    [super prepareForReuse];
}

- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary *)change
                       context:(void *)context
{
    if (object == [self possession]) {
        if ([keyPath isEqualToString:kPossessionNameKeyPath]) {
            [[self textLabel] setText:[change
                                       objectForKey:NSKeyValueChangeNewKey]];
        }
        else if ([keyPath isEqualToString:kPossessionValueKeyPath]) {
            [[self detailTextLabel] setText:[[change
                                              objectForKey:NSKeyValueChangeNewKey] stringValue]];
        }
    }
}
@end
```

当我们设置 `possession` 的值时，会为它的 `name` 和 `value` 属性添加一个观察者。每次设置观察者时，我们也将 `isObservingPossession` 的值设置为 `YES`，以便跟踪是否需要移除它。如果不这样做，可能会因移除未注册的观察者而导致应用崩溃。添加观察者时，我们指定了特定的选项，以便在初始值发生改变时立即收到通知，同时也能监控后续的任何变化。最后，在观察到变化时，我们会在表格视图单元格中设置对应的值。这个类已完成，请保存你的工作并打开 `PossessionListViewController.m`。在文件顶部的其他 `#import` 声明中添加一行，以导入你的表格视图单元格类：

```objective-c
#import "PossessionListTableViewCell.h"
```

接下来，修改 `tableView:cellForRowAtIndexPath:` 方法，使其使用新的表格视图单元格：

```objective-c
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";
    NSString *cellIdentifier = @"PossessionCell";
    
    [www.it-ebooks.info](http://www.it-ebooks.info/)
    
    // 第 4 章：在应用中保存内容（此行是重复的标题，可能是格式错误）
    
    UITableViewCell *cell = [tableView
                             dequeueReusableCellWithIdentifier:CellIdentifier];
    PossessionListTableViewCell *cell = (PossessionListTableViewCell *)
    [tableView dequeueReusableCellWithIdentifier:cellIdentifier];
    
    if (cell == nil) {
        cell = [[PossessionListTableViewCell alloc]
                initWithStyle:UITableViewCellStyleValue1
                reuseIdentifier:cellIdentifier];
    }
    
    Possession *possession = [self possessionAtIndex:[indexPath row]];
    [cell setPossession:possession];
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];
    
    return cell;
}
```

运行应用并编辑一个物品。你会看到列表中的表格视图单元格在编辑后自动更新。虽然看起来和之前一样，但效率更高了，因为只有需要变更的部分才会更新，而之前每次都会重新加载所有屏幕上的表格视图单元格。对于大型应用来说，如果表格视图单元格结构复杂、创建时需要大量处理器时间，这种优化决定了应用是运行流畅还是缓慢且不受欢迎。

使用键值观察（KVO）是控制应用数据流转的绝佳方式。通过 KVO 来更新 UI 元素，可以将更新模型对象的代码与更新 UI 的代码解耦；现在设置好这些表格视图单元格后，我们可以随意修改 `Possession` 类的实例，而无需再去手动更新表格视图单元格。不过我们仍然有一个问题：新条目无法被添加。为了解决这个问题，我们可以修改详情视图控制器的代理方法，以通知表格视图这些变更。打开 `PossessionListViewController.m`，并按如下方式修改 `possessionDetailViewController:didEditPossession:` 方法：


```objectivec
- (void)possessionDetailViewController:(PossessionDetailViewController *)detailViewController
                  didEditPossession:(Possession *)possession
{
    if ([_possessions containsObject:possession] == NO) {
        [_possessions addObject:possession];
        NSIndexPath *newIndexPath = [NSIndexPath
            indexPathForRow:[_possessions indexOfObject:possession]
                  inSection:0];
        NSArray *indexPaths = [NSArray arrayWithObject:newIndexPath];
        [[self tableView] insertRowsAtIndexPaths:indexPaths
                                withRowAnimation:UITableViewRowAnimationAutomatic];
    }
}
```

现在，当你添加一个新项目时，表格视图会自动为其添加一行，而无需重新加载其他行，这样效率更高。这在委托方法中效果很好，因为我们已经在这两个视图控制器之间建立了连接。如果没有这种连接，当新项目被添加时，我们又该如何得到通知呢？答案是另一种在应用中传递数据的方式：通知。

## 通知

正如我们提到的，优秀代码设计中的一个常见模式是解耦。尽管委托协议非常适合这一点，但它们仍然要求对象与其委托之间是一对一的关系。而`通知`则允许你在整个应用中广播消息，而无需知道哪些对象正在监听。通知由 `NSNotificationCenter` 类处理，这是一个单例——即设计为只有一个实例的对象。你可以像这样访问该单例实例：

```objectivec
NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
```

### 注册通知

与 Cocoa Touch 中的许多其他 API 一样，使用 `NSNotificationCenter` 通常需要设置一个目标（target）和一个动作（action）；在这里，观察者就是目标。要接收通知，类似于 KVO，你需要先注册它：

```objectivec
static NSString * const kNotificationName = @"notificationName";
[nc addObserver:self
       selector:@selector(handleNotification:)
           name:kNotificationName
         object:nil];
```

这四个参数第一个是观察者，接着是一个选择器，指定当通知触发时要发送给观察者的消息。接下来是通知名称。这只是一个标识通知的字符串。在你自己的应用中，最好像前面 `kNotificationName` 那样将名称定义为一个字符串常量，而不是每次使用时重新输入；花几个小时追查一个 bug，最后却发现只是拼错了通知名称，这相当尴尬（虽然我没干过这种事！）。最后一个参数允许你指定要从哪个对象接收通知。通常你会在这里传入 `nil`，这会告诉 `NSNotificationCenter` 在任何对象触发通知时都发送消息。

> **注意:** 就像键值观察（KVO）一样，在 `NSNotificationCenter` 的观察者被释放之前移除它非常重要，否则当已释放的对象被发送消息时，你的应用有崩溃的风险。你通常会在 `dealloc` 方法中执行此操作。

你可能已经注意到，示例代码中传递给 `NSNotificationCenter` 的选择器以冒号结尾，表示它有一个参数。该参数类型为 `NSNotification`，其中包含一些关于通知的有用信息。`通知`有三个实例方法可以用来获取这些信息：`name`，通知的名称；`object`，发布通知的对象；以及 `userInfo`，一个包含发布通知时附带的所有键值对的 `NSDictionary`。`userInfo` 字典是使用 `NSNotifications` 最有用的特性之一，许多系统通知都通过这种方式提供有用的信息。当你创建自己的通知时，也可以构造自己的 `userInfo` 字典，以便传递所需的数据。

### 发布你自己的通知

当你想要发布通知时，无需自己创建 `NSNotification` 对象，只需使用 `NSNotificationCenter` 的便捷方法之一：`postNotificationName:object:` 或 `postNotificationName:object:userInfo:`。第一个参数与注册观察者时一样，是通知的名称；第二个参数是发布通知的对象（几乎总是 `self`）。如果你愿意，也可以创建自己的通知对象；只需改用 `postNotification:` 方法即可。创建通知的代码可能如下所示：

```objectivec
NSDictionary *userInfo = [NSDictionary dictionaryWithObject:@"Fido"
                                                    forKey:@"dogName"];
[nc postNotificationName:@"notificationName"
                  object:self
                userInfo:userInfo];
```

使用通知的一个强大之处在于，你无需了解接收通知的对象。与委托消息不同，你可以让多个对象接收同一个通知，因此，如果你的应用有多个显示同一对象内容的视图控制器，你可以发送一个通知来告知它们该对象已更新。

### 常见的系统通知

在 iOS 中，有一些非常有用的通知可供你的对象监听。其中最重要的是 `UIApplicationDidReceiveMemoryWarningNotification`，每当系统内存不足时就会发送该通知。你在视图控制器中已经见过它，但如果你在视图控制器之外有一个使用大量内存的对象（如图片缓存），就可以使用这个通知来指示何时清理该内存。另一个有用的通知是 `UIApplicationSignificantTimeChangeNotification`，它会在夏令时生效等情况下被发布。任何日历类应用都应该响应这个通知。当然，iOS 中还有更多有用的通知，但这里仅仅罗列它们是不够的。相反，你可以参考开发者文档以获取更多详细信息。

> **注意:** 从前面两个例子可以看出，Apple 对通知名称的命名规范似乎使其尽可能长。如果你在其他平台上曾尝试在 80 或 100 字符宽的屏幕上保持代码整洁，那么在使用 Cocoa Touch 时可能很难维持这种风格。Apple 宁可冗长也不愿模糊，因此长名称很常见。

## 单例

`NSNotificationCenter` 是单例的一个绝佳示例。简单来说，单例是一个设计成只有一个实例且永久存在的对象。单例对于任何为多个对象协调数据的对象都非常有用，它作为一组通用功能的单个引用点。另一个单例的例子是 `NSFileManager`，它用于访问设备的文件系统，这也是你在本章后面要做的事情。许多程序员对单例评价不高，因为过度使用它存在风险；如果你的单例从未被释放，它们会无限期地占用内存，因此使用过多会对性能产生不利影响。在 Cocoa Touch 中，单例被频繁使用，但在使用之前，请务必考虑单例在你的代码中是否合理。

一个典型的单例拥有一个通过类方法访问的共享实例。一个原型单例可能看起来像这样：

```objectivec
@interface Singleton : NSObject
+ (id)sharedInstance;
@end

@implementation Singleton
static Singleton *_sharedInstance = nil;

+ (id)sharedInstance
{
    if (_sharedInstance == nil) {
        _sharedInstance = [[self alloc] init];
    }
    return _sharedInstance;
}
@end
```


**注意：** 尽管我们在之前的代码中，在声明行将 `_sharedInstance` 初始化为 `nil`，但如果你在使用 ARC，这并非严格必要，因为默认情况下它会被初始化为 `nil`。如果你没有使用 ARC，则需要将其初始化为 `nil`，因为在声明之后，它会立即指向一个垃圾值。

以这种方式创建的单例永远不会被释放，因为 `_sharedInstance` 变量将始终持有对它的引用。借助 ARC 的弱引用，你可以将其定义为弱引用，从而允许它在不使用时被释放，但如果你的单例创建成本很高（从性能角度考虑），那么这样做可能得不偿失。

有些网站可能会指导你重写单例的 `retain`、`release` 和 `retainCount` 方法，以禁止创建多个实例。虽然在 ARC 下这是不可能的，因为你无法重写这些方法，但在 ARC 之前，这也并非最佳建议。有时，出于性能原因，你可能希望创建一个完全不是单例的 `NSNotificationCenter` 或 `NSFileManager` 的独立实例；在这种情况下，使用常规的 `alloc` 和 `init` 方法返回的对象，其生命周期与任何其他对象一样。

[www.it-ebooks.info](http://www.it-ebooks.info/)

