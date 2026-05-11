# 初始化 Core Data 数据模型

在查看 Apple 的 Core Data 示例代码时，我们发现所有初始化代码都位于应用代理类中。不过，通常的做法是创建一个单例模型类，并在该单例实例中一次性完成 Core Data 模型的初始化。这样，项目中的任何类都可以通过模型单例直接访问托管对象上下文。

让我们先创建一个单例模型类。在 `data` 组中新建一个 `NSObject` 的子类，并将类命名为 `BRDModel`。为了设置我们的单例类，我们将仅通过一个名为 `sharedInstance` 的类方法来提供对模型的访问。`BRDModel` 的 `sharedInstance` 方法会创建 `BRDModel` 的唯一实例，如果实例已存在则直接返回。首先，在 `BRDModel.h` 中定义该类方法：

```
#import <Foundation/Foundation.h>
@interface BRDModel : NSObject

+ (BRDModel*)sharedInstance;

@end
```

现在切换到 `BRDModel.m`，实现这个新的类方法：

```
#import "BRDModel.h"

@implementation BRDModel

static BRDModel *_sharedInstance = nil;
+ (BRDModel*)sharedInstance
{
    if( !_sharedInstance ) {
               _sharedInstance = [[BRDModel alloc] init];
}
       return _sharedInstance;
}

@end
```

`sharedInstance` 方法初始化了其自身类的一个实例。这是在 Objective-C 中实现单例的常用技术。每当我们需要在应用中访问模型时，只需引用 `[BRDModel sharedInstance]` 即可。接下来，我们从本章前面创建的 Apple 自带的 Master-Detail 应用中获取 Core Data 初始化代码。找到 `AppDelegate.h` 头文件，复制持久化存储协调器、托管对象模型和托管对象上下文的声明，并直接粘贴到 `BRDModel.h` 头文件中，如下所示：

```
#import <Foundation/Foundation.h>

@interface BRDModel : NSObject

+ (BRDModel*)sharedInstance;

@property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;
@property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;
@property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;

@end
```

切换到 `BRDModel.m` 源文件，并像在 `AppDelegate.m` 源文件中一样，合成这三个新的 Core Data 属性：

```
@implementation BRDModel

@synthesize managedObjectContext = _managedObjectContext;
@synthesize managedObjectModel = _managedObjectModel;
@synthesize persistentStoreCoordinator = _persistentStoreCoordinator;
```

*注意：在此处我们手动添加了合成代码，以确保每个属性都指向一个带下划线的实例变量。Xcode 的编译器不会为 `readonly` 属性自动执行此操作。*

然后，从 Master-Detail 项目的 `AppDelegate.m` 中 Apple 自带的代码里，复制三个 Core Data 访问器方法以及 `applicationDocumentsDirectory` 辅助私有方法的实现。如果愿意，可以移除注释，但更重要的是，将两处 `CoreDataExample` 字符串替换为我们 *Birthday Reminder* Core Data 模型的名称 `BirthdayReminder`，如下方高亮代码所示：

```
- (NSManagedObjectContext *)managedObjectContext
{
    if (_managedObjectContext != nil) {
        return _managedObjectContext;
    }

    NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
    if (coordinator != nil) {
        _managedObjectContext = [[NSManagedObjectContext alloc] init];
        [_managedObjectContext setPersistentStoreCoordinator:coordinator];
    }
    return _managedObjectContext;
}

// 返回应用的托管对象模型。
// 如果模型不存在，则从应用的模型文件中创建。
- (NSManagedObjectModel *)managedObjectModel
{
    if (_managedObjectModel != nil) {
        return _managedObjectModel;
    }
    NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"BirthdayReminder"
    withExtension:@"momd"];
    _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
    return _managedObjectModel;
}

// 返回应用的持久化存储协调器。
// 如果协调器不存在，则创建它并添加应用的存储。
- (NSPersistentStoreCoordinator *)persistentStoreCoordinator
{
    if (_persistentStoreCoordinator != nil) {
        return _persistentStoreCoordinator;
    }

    NSURL *storeURL = [[self applicationDocumentsDirectory]
URLByAppendingPathComponent:@"BirthdayReminder.sqlite"];

    NSError *error = nil;
    _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc]
initWithManagedObjectModel:[self managedObjectModel]];
    if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType
configuration:nil URL:storeURL options:nil error:&error]) {
        NSLog(@"未解决的错误 %@, %@", error, [error userInfo]);
        abort();
    }

    return _persistentStoreCoordinator;
}

#pragma mark - 应用的 Documents 目录

// 返回应用 Documents 目录的 URL。
- (NSURL *)applicationDocumentsDirectory
{
    return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory
inDomains:NSUserDomainMask] lastObject];
}
```

现在正是检查应用是否能正常编译的好时机，尽管你还看不到任何变化，因为我们仍处于 Core Data 实现的准备阶段。



