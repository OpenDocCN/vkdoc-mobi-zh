# 第 23 章 ■ 单例模式

`单例`是只有一个实例的类。它们通常是向单个（通常是全局的）设施或资源提供面向对象接口的对象。管理数据库对象缓存的类会希望创建自身的一个单进程实例，以便协调整个应用程序的所有对象缓存。提供文件管理方法的类可以创建一个单例对象，供所有其他类用于与文件系统交互。后者也可以通过定义一个仅包含类方法的类来实现，但这种模式在 Objective-C 中并不常见。

单例对象通常由一个幂等的类方法或 C 函数创建，该方法负责创建和维护该类的唯一实例。这本质上是一种特殊的工厂方法，在第 22 章中有详细描述。与工厂方法类似，单例可以通过类方法或`类簇`的退化变体来实现。

### 实现单例

以下是 Cocoa 框架中单例对象的示例：

- `NSApplication`
- `NSFileManager`
- `NSDistributedNotificationCenter`
- `NSWorkspace`

单例有时在启动时创建，但通常通过一个幂等方法提供，该方法返回单例的引用，并在必要时首次创建它。

在 Cocoa 框架中，以下类方法返回上述类对应的单例：

- `[NSApplication sharedApplication]`
- `[NSFileManager defaultManager]`
- `[NSDistributedNotificationCenter defaultCenter]`
- `[NSWorkspace sharedWorkspace]`

单例模式的典型实现如代码清单 23-1 所示。

### 代码清单 23-1. 单例模式

**Java**

```java
public class CommandCenter {
    private static CommandCenter sharedCommandCenter;
    public static CommandCenter getCommandCenter() {
        if (sharedCommandCenter == null) {
            sharedCommandCenter = new CommandCenter();
        }
        return sharedCommandCenter;
    }
}
```

**Objective-C**

```objc
@interface CommandCenter : NSObject
+ (CommandCenter*)sharedCommandCenter;
@end

static CommandCenter *SharedCommandCenter;

@implementation CommandCenter
+ (CommandCenter*)sharedCommandCenter {
    if (SharedCommandCenter == nil) {
        SharedCommandCenter = [CommandCenter new];
    }
    return SharedCommandCenter;
}
@end
```

在 Java 或 Objective-C 中可以实现完全相同的模式。不过，Objective-C 为典型的单例设计模式提供了两种替代方案。第一种是利用类的`+initialize`方法，第二种是`类簇`的变体。这两种方法将在接下来的章节中探讨。

### 惰性单例

大多数单例是惰性的；它们是在第一次被请求时才自发构建的。除了自行实现这种机制外，另一种方法是利用类本身的惰性初始化，如第 21 章所示。基本上，通过重写类的`+initialize`方法，单例对象会在该类首次接收消息时被创建。代码清单 23-2 展示了`CommandCenter`类的重写版本。

### 代码清单 23-2. 惰性单例模式

```objc
@interface CommandCenter : NSObject
```



```objc
+ (CommandCenter*)sharedCommandCenter;

@end

static CommandCenter *SharedCommandCenter;

@implementation CommandCenter

+ (void)initialize

{

SharedCommandCenter = [CommandCenter new];

}

+ (CommandCenter*)sharedCommandCenter

{

return SharedCommandCenter;

}

@end
```

类的`+initialize`方法总是类收到的第一条消息。它是创建单例的便捷位置。它也简化了类中的代码；任何类方法都可以直接引用`SharedCommandCenter`变量（例如，不必每次都使用`[CommandCenter sharedCommandCenter]`），并确保它已被初始化。

请注意，`+initialize`不会像 Java 初始化语句那样在应用程序启动时或类被加载时调用。`+initialize`消息在类首次收到消息时发送。如果该类从未被使用，则永远不会发送`+initialize`消息。

### 单例工厂

工厂模式可以被重新利用，将类簇转换为单例工厂。在第 22 章中，你看到了类的初始化器如何用另一个类替换被请求的类。同样的技术也可以用于返回一个已有的对象。为了实现单例模式，削弱初始化器，使其每次返回同一个对象。清单 23-3 中的代码使用类的初始化器实现了一个单例。

**清单 23-3**. 单例工厂模式

```objc
@interface CommandCenter : NSObject

@end

static CommandCenter* SharedCommandCenter;

@implementation CommandCenter

- (id)init

{

self = [super init];

if (self!=nil) {

if (SharedCommandCenter!=nil)

return SharedCommandCenter;

…

SharedCommandCenter = self;

}

return self;

}

@end
```

`CommandCenter`类的初始化器方法实现了单例模式。第一个创建的`CommandCenter`实例（使用`[[CommandCenter alloc] init]`）被保存在全局变量中。任何后续尝试创建另一个`CommandCenter`实例的操作都会返回原始实例。

单例模式的这个实现具有类簇的所有优点和缺点。主要优点是客户端代码不必使用特殊消息来获取单例，甚至不必知道该类是单例。它只需创建对象并使用它。另一个优点是类作者不必采取任何特殊措施来阻止客户端尝试创建类的额外实例。

> **注意**：出于显而易见的原因，你的单例类可能不会遵循`NSCopying`协议。如果需要遵循（例如，用作`NSDictionary`中的键），请重写`-copyWithZone:`方法并让它返回`self`。

这种模式的一个变体是从不可变对象池中返回对象。一个封装字母表中字母属性的`Letter`类可以为每个字母实现一个单例工厂。每次调用`[[Letter alloc] initWithLetter:'a']`都将返回一个单例的`'a'`对象，而每次`[[Letter alloc] initWithLetter:'b']`都将返回一个单例的`'b'`对象，依此类推。这是实现享元设计模式的一种替代方案。

### 总结

单例模式在 Java 和 Objective-C 中都极为常见。Objective-C 提供了一些有趣的替代方案，可以简化其实现。也可以将类簇转换为单例工厂，隐藏其单例性质。

---

