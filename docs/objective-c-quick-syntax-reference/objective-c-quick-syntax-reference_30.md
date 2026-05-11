# 25. 单例模式

摘要

单例模式是一种设计模式，它确保一个类只能有一个实例。通常，当你定义一个类时，你期望使用该类的多个实例。但在某些设计中，这种做法是没有意义的。

## 单例模式定义

单例模式是一种设计模式，它确保一个类只能有一个实例。通常，当你定义一个类时，你期望使用该类的多个实例。但在某些设计中，这种做法是没有意义的。

例如，一个应用程序可能只需要一个对文件系统的引用（因为只有一个文件系统）。或者，应用程序有一个需要保持同步的数据模型，因此你希望确保只有一个类的实例可用。

要实现单例模式，你需要创建一种特殊类型的构造方法，然后只使用这个构造方法来获取单例对象的引用。

### 单例接口

第一步是编写接口代码。假设你要创建一个名为 `AppSingleton` 的类，它将作为你的单例。以下是接口的编写方式：

```
#import <Foundation/Foundation.h>

@interface AppSingleton : NSObject

+ (AppSingleton *)sharedInstance;

@end
```

上述代码是一个返回 `AppSingleton` 实例的类方法。

### 单例实现

要实现这个单例，你需要一个静态变量以及你在接口中定义的类方法 `sharedInstance` 的具体实现。

```
#import "AppSingleton.h"

@implementation AppSingleton

static AppSingleton *singletonInstance = nil;

+ (AppSingleton *)sharedInstance{
    @synchronized(self){
        if (singletonInstance == nil)
            singletonInstance = [[self alloc] init];
        return(singletonInstance);
    }
}

@end
```

静态实例是一个名为 `singletonInstance` 的 `AppSingleton` 类型变量，初始设置为 nil。在 `sharedInstance` 方法中，你检查 `singletonInstance`；如果它为 nil，则创建一个新实例。无论哪种情况，你都将这个实例返回给调用者。

该方法中的代码被 `@synchronized(self)` 块包含。这用于锁定代码，使得同一时间只有一个线程可以使用这些代码行。这确保了即使有多个线程，你也只会拥有这个单例的一个实例。

### 引用单例

当你需要一个单例对象时，必须使用返回该实例的方法。这也就是你编写的方法。

如果你想使用 `AppSingleton` 类，可以这样做：

```
AppSingleton *ap = [AppSingleton sharedInstance];
```

这用于替代通常创建对象时使用的 `alloc` 和 `init` 模式。你可以从应用程序中的任何类或文件中执行此操作，每一个都会得到该类的同一个实例。唯一的注意事项是，你必须使用单例的构造方法；虽然你仍然可以使用 `alloc` 和 `init`，但这样做会破坏这种模式，因为这些方法仍然可以创建多个对象，而你创建的构造方法则只能创建一个对象。

