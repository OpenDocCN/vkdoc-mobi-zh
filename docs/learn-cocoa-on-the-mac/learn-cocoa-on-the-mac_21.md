# 保存谓词

在收工之前，让我们再添加最后一点润色，让用户使用 `QuoteMonger` 时拥有更友好的体验。目前，用户每次启动应用，它都会加载我们那个略显简陋的默认查询。如果它能显示上次运行时的最后查询，岂不是更好？实际上，这简直是小菜一碟。`NSPredicate` 有一个很方便的方法叫 `predicateFormat`，它能以字符串形式返回谓词的值。因此，当用户退出应用时，我们可以使用 `NSUserDefaults` 保存当前 `searchPredicate` 的字符串表示形式，并在应用启动时检查是否有已保存的字符串。打开 `QMAppDelegate.m`，对其实现的第一部分进行如下修改（只需添加所有加粗的行）：

```
#import "QMAppDelegate.h"

@implementation QMAppDelegate

@synthesize persistentStoreCoordinator = _persistentStoreCoordinator;
@synthesize managedObjectModel = _managedObjectModel;
@synthesize managedObjectContext = _managedObjectContext;

#define DEFAULT_PREDICATE @"(quoteText CONTAINS[cd] 'missed') OR " \
                           "(character CONTAINS[cd] 'kramer')"
#define STORED_PREDICATE_KEY @"storedPredicateFormat"

- init
{
  if ((self = [super init])) {
    NSString *format = [[NSUserDefaults standardUserDefaults]
        objectForKey:STORED_PREDICATE_KEY];
    if (format)
      self.searchPredicate = [NSPredicate predicateWithFormat: format];
    else
      self.searchPredicate = [NSPredicate predicateWithFormat: DEFAULT_PREDICATE];
  }
  return self;
}

- (void)applicationWillTerminate:(NSNotification *)aNotification
{
  NSString *format = [self.searchPredicate predicateFormat];
  [[NSUserDefaults standardUserDefaults] setObject:format forKey:STORED_PREDICATE_KEY];
}
```

我们首先定义一个字符串，它将用作键，通过 `NSUserDefaults` 在用户偏好设置中存储和检索谓词。然后，我们增强 `init` 方法，使其查找已存储的谓词。它首先检查是否存在已存储的谓词字符串，如果存在，则从该格式字符串创建新的谓词并填充到 `searchPredicate` 实例变量中。如果没有存储的谓词，则创建默认谓词。

最后，我们实现了 `applicationWillTerminate:` 方法。当用户退出应用时，该方法会被自动调用，给我们一个进行最终清理的机会。在这里，我们将当前的搜索参数（维护在 `searchPredicate` 实例变量中）转换为字符串，并将该字符串保存到用户的偏好设置中，以便下次用户运行应用时，相同的搜索会再次出现。

保存、运行，然后修改搜索条件。退出应用并再次运行，之前的搜索条件会再次显示出来。

## 总结

本章展示了如何使用 `NSPredicate` 来缩小 Core Data 结果集的基础知识。我们了解了如何通过代码、在 Xcode 中，或通过用户与 `NSPredicateEditor` 的交互来构建 `NSPredicate`。我们甚至还了解到了如何将这些谓词保存起来以备后用，就像 iTunes 和 Mail 的智能播放列表和智能文件夹那样。这些技巧可以帮助你轻松地为自己的应用带来新层次的功能。

第 8 章到第 10 章介绍了启动和运行 Core Data 所需的主要概念。现在是时候转向其他话题了。不过，我们并不会就此离开 Core Data，它将在后续的一些练习中继续发挥作用，尽管程度不同。由于篇幅有限，我们无法将每个示例都变成一个完整的应用程序。但即使在不使用 Core Data 的情况下，你也可以思考一下它如何融入我们在后续章节中演示的内容。既然你已经掌握了 Core Data，那么在应用程序开发的某些方面，你可能会对之前用不同方式解决的问题产生全新的认识。说到认识，这正是我们将在第 11 章中探讨的方向，我们将了解 Cocoa 以及其他框架中一些最突出、最广泛使用的视图组件：窗口、菜单和表单。

