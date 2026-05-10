# 在应用委托中添加谓词

首先，在应用委托中添加一个新属性。打开 `QMAppDelegate.h` 文件，该文件看起来与为 MythBase 生成的默认应用委托头文件一致。添加一个新属性，名为 `searchPredicate`。现在的接口声明应如下所示（新代码行以粗体显示）：

```
@interface QMAppDelegate : NSObject <NSApplicationDelegate>

@property (assign) IBOutlet NSWindow *window;
@property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;
@property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;
@property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;
@property (strong) NSPredicate *searchPredicate;

- (IBAction)saveAction:(id)sender;

@end
```

现在切换到 `QMAppDelegate.m` 文件，并在靠近顶部的位置，紧跟在 Xcode 生成的 `@synthesize` 指令之后，添加以下代码。新代码行以粗体显示。

```
@implementation QMAppDelegate

@synthesize persistentStoreCoordinator = _persistentStoreCoordinator;
@synthesize managedObjectModel = _managedObjectModel;
@synthesize managedObjectContext = _managedObjectContext;

#define DEFAULT_PREDICATE @"(quoteText CONTAINS[cd] 'space') OR " \
                          "(character CONTAINS[cd] 'Knight')"

- (id)init
{
    if ((self = [super init])) {
        self.searchPredicate = [NSPredicate predicateWithFormat:DEFAULT_PREDICATE];
    }
    return self;
}
```

`init` 方法仅为 `searchPredicate` 属性创建了一个默认值。现在，让我们将控制器与这个新谓词关联起来。回到 Interface Builder 画布，选中 FoundQuotes 控制器，并打开属性检查器。选中“获取谓词”文本视图中的所有文本并删除，然后切换到绑定检查器。在“控制器内容参数”部分，展开“过滤谓词”绑定信息。在弹出的菜单中选择“应用委托”，在“模型键路径”组合框中输入 `searchPredicate`，然后按 Return 键启用绑定。

保存工作，回到 Xcode，然后点击“运行”。现在你应该会看到一组不同的结果：由名为 `Knight` 的角色所说或包含单词 `space` 的引用。如果没有匹配的结果，请添加一条包含这些特征之一的引用，以测试搜索窗口。

