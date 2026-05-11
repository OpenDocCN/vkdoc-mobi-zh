# 内存管理选项

使用 `Objective-C`，你在实现内存管理时有三种选择：使用引用计数的手动内存管理、`自动引用计数 (ARC)`，或垃圾回收（`iOS` 应用程序除外）。

手动内存管理是任何 `Objective-C` 程序都可以使用的。然而，正如你将在本章中看到的，手动内存管理是一项繁琐且耗时的任务。

`自动引用计数`适用于 `Mac` 和 `iOS`，但仅限于较新版本的操作系统。通常，如果你在开发新应用程序，我建议使用 `ARC`，因为它高效且同时适用于 `Mac` 和 `iOS`。如果你仅开发 Mac 应用，垃圾回收则是一个可选方案。

**注意：** 在本章中，你将了解如何执行手动内存管理以及垃圾回收。由于 `ARC` 已在其他章节的示例中使用，因此本章不再专门介绍。

## 8.2 设置一个不使用 ARC 的应用程序

### 问题

你想要设置一个不使用 `ARC` 来管理内存的应用程序。

### 解决方案

当你设置一个新的 `Xcode` 项目时，会看到一个标题为“为你的新项目选择选项”的屏幕。其中一个选项是一个复选框，写着“使用自动引用计数”。确保此选项未被勾选。

你可以这样为 Mac 应用程序、Mac 命令行应用程序或任何 `iOS` 应用程序设置项目。出于本章中大部分示例（垃圾回收示例除外）的目的，我将使用一个 `iOS` 应用程序。

### 工作原理

`Xcode` 根据你提供的设置来配置项目。当你在选项屏幕中选择不使用 `ARC` 时，`Xcode` 会记住在编译项目时使用指示不使用 `ARC` 的编译器设置。`Xcode` 还允许你发送内存管理所需的特定消息。

这些消息是 `retain`、`release`、`autorelease` 和 `dealloc`。如果你尝试在 `ARC` 项目中使用这些消息，将会出现编译器错误，但在非 `ARC` 项目中，你将使用这些消息来实现引用计数系统。

事实上，如果你查看 `AppDelegate.m` 文件，会看到一个 `Xcode` 自动为你编写的 `dealloc` 方法示例。这个 `dealloc` 方法包含了一些内存管理代码，我将在下一个示例中介绍。代码见列表 8-1 至 8-3。

```
- (void)dealloc{
    [_window release];
    [super dealloc];
}
```

### 代码

**列表 8-1.** *main.m*

```
#import <UIKit/UIKit.h>

#import "AppDelegate.h"

int main(int argc, char *argv[]){
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate
class]));
    }
}
```

**列表 8-2.** *AppDelegate.h*

```
#import <UIKit/UIKit.h>

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@end
```

**列表 8-3.** *AppDelegate.m*

```
#import "AppDelegate.h"

@implementation AppDelegate

@synthesize window = _window;

- (void)dealloc{
    [_window release];
    [super dealloc];
}

- (BOOL)application:(UIApplication *)application
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]]
autorelease];
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    return YES;
}

@end
```

### 用法

你可以像使用其他项目一样使用此项目。现在，只需关注一下 `Xcode` 为你创建的应用委托中的一些差异。这里包含了 `dealloc` 方法。如果你仔细查看 `application:applicationDidFinishLaunchingWithOptions:` 方法中 `window` 属性的构造函数，可以看到 `window` 对象发送了 `autorelease` 消息。这两者都是你在接下来几个内存管理示例中将要执行的操作示例。

## 8.3 使用引用计数管理内存

### 问题

你想要在你的应用程序中使用一个对象，并需要确保该对象的内存得到有效管理。

### 解决方案

当使用消息 `alloc`、`new` 或 `copy` 创建对象时，构造代码所在实体便声称拥有该对象的所有权，并且该对象的引用计数被设置为 1。当不再需要该对象时，所有者负责向该对象发送 `release` 消息。



### 8.3 手动内存管理

### 工作原理

当你创建并使用了某个对象后，发送一条 `release` 消息。例如，如果我使用 `alloc` 和 `init` 创建了一个 `NSObject`，接着使用该对象。使用完毕后，我发送 `release` 消息让 Objective-C 销毁该对象。

```
NSObject *obj = [[NSObject alloc] init];
NSLog(@"obj's description is %@", [obj description]);
[obj release];
```

在第一行中，我使用 `alloc` 创建了一个引用计数为 1 的 `NSObject`。当我使用该对象向日志输出一条消息时，引用计数依然为 1。在第三行代码中，当我发送 `release` 消息后，对象的引用计数变为 0，Objective-C 会自动销毁该对象。

你需要做的就是这些。有效的内存管理归结为一致性，并严格遵守一系列简单规则。这段代码体现了以下规则：

**规则：** 务必始终将 `alloc`、`new` 和 `copy` 与一条 `release` 消息配对。

虽然在此示例中并不真正需要，但可以增加对象的引用计数。你可以通过向对象发送 `retain` 消息来实现。`retain` 消息表示某个实体声明了对该对象的所有权。如果你向 `obj` 发送了一条 `retain` 消息，`obj` 的引用计数就会增加到 2。

```
NSObject *obj = [[NSObject alloc] init];
NSLog(@"obj's description is %@", [obj description]);
[obj retain];
[obj release];
```

实际上，你是在声明对该对象的双重所有权。如果就此保留这段代码，就会产生问题。对象的引用计数初始为 1，发送 `retain` 消息后变为 2。发送 `release` 消息后，引用计数又降回 1。但是，如果你保持代码不变，由于引用计数从未达到 0，Objective-C 将永远无法销毁该对象。

当这种情况发生时，就会导致内存泄漏。如前所述，内存泄漏是由那些占用内存且从未让系统回收内存的对象引起的。如果不加以控制，内存泄漏可能会导致应用程序变慢或崩溃。

要解决这个问题，你必须在结束使用 `obj` 时再发送一条 `release` 消息。

```
NSObject *obj = [[NSObject alloc] init];
NSLog(@"obj's description is %@", [obj description]);
[obj retain];
[obj release];
[obj release];
```

这引出了另一条内存管理规则。

**规则：** 务必始终将每次 `retain` 与一条 `release` 配对。

基本上，你要确保在结束使用对象时，其引用计数为零。代码见 清单 8-4。

#### 代码

**清单 8-4.** *main.m*

```
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

int main(int argc, char *argv[]){
    @autoreleasepool {

        NSObject *obj = [[NSObject alloc] init];

        NSLog(@"obj's description is %@", [obj description]);

         [obj retain];
         [obj release];
         [obj release];

return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

#### 用法

你可以将 清单 8-4 中的代码包含到你自己的 `main` 函数中来测试。这段代码本身除了写入日志外并没有做太多事情，但在手动管理内存时，一旦你使用对象，就需要遵循这种模式。

## 8.4 为自定义类添加内存管理

### 问题

你有一些自定义类可能会声明对对象的所有权，并且你希望确保它们能够正确地管理内存。

### 解决方案

在自定义类中，内存管理成为问题的两种情况主要有：属性的 getter 和 setter 代码，以及 `dealloc` 方法。属性需要配置为声明对分配给持有对象引用的实例变量的对象的所有权。`dealloc` 方法是一个特殊方法，Objective-C 会在对象被销毁之前调用它。你需要为每个自定义类实现一个 `dealloc` 方法。

### 工作原理

本方法中使用的示例扩展了你在方法 1.4 和 1.6 中编写的 `Car` 类。你将向这个类添加必要的代码，以确保你遵循了方法 8.3 中提到的内存管理规则。

为了防止你忘记，以下是 `Car` 类的使用方法。注意，这次我添加了一条 `release` 消息，因为你没有使用 ARC。

```
#import <UIKit/UIKit.h>
#import "AppDelegate.h"
#import "Car.h"

int main(int argc, char *argv[]){
    @autoreleasepool {

        [Car writeDescriptionToLogWithThisDate:[NSDate date]];

        Car *c = [[Car alloc] init];

        c.name = @"New Car Name";

        [c writeOutThisCarsState];

        [c release];

return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

##### dealloc 方法

`dealloc` 方法由 Objective-C 在对象被销毁之前调用。`dealloc` 的目的是让你有机会在所有者对象被销毁之前释放你所拥有的任何对象。你需要为你创建的每个自定义类重写 `dealloc` 方法。你释放每个本地实例，然后将它们设为 `nil`。

以下是你如何为 `Car` 类编写 `dealloc` 方法：

```
-(void)dealloc{
    NSLog(@"%@'s dealloc is executing", self.name);
    [super dealloc];
    [name_ release];
    name_ = nil;
}
```

如你所见，你必须首先向超类发送 `dealloc` 方法，因为父对象所持有的任何东西也必须被释放。在这里，释放任何标记为 `strong` 引用的对象非常重要。你还应该在此处将任何对象设为 `nil`。代码见 清单 8-5 至 8-7。

#### 代码

**清单 8-5.** *Car.h*

```
#import <Foundation/Foundation.h>

@interface Car : NSObject{
@private
    NSString *name_;
}

@property(strong) NSString *name;

+(void)writeDescriptionToLogWithThisDate:(NSDate *)date;

-(void)writeOutThisCarsState;

@end
```

**清单 8-6.** *Car.m*

```
#import "Car.h"

@implementation Car

-(void)setName:(NSString *)name{
    [name retain];
    [name_ release];
    name_ = name;
}

-(NSString *) name{
    return name_;
}

+(void)writeDescriptionToLogWithThisDate:(NSDate *)date{
        NSLog(@"Today's date is %@ and this class represents a car", date);
}

-(void)writeOutThisCarsState{
        NSLog(@"This car is a %@", self.name);
}

-(void)dealloc{
    NSLog(@"%@'s dealloc is executing", self.name);
    [super dealloc];
    [name_ release];
    name_ = nil;
}

@end
```

**清单 8-7.** *main.m*

```
#import <UIKit/UIKit.h>
#import "AppDelegate.h"
#import "Car.h"

int main(int argc, char *argv[]){
    @autoreleasepool {

        [Car writeDescriptionToLogWithThisDate:[NSDate date]];

        Car *c = [[Car alloc] init];

        c.name = @"New Car Name";

        [c writeOutThisCarsState];

        [c release];

return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

#### 用法

要使用这段代码，请将清单中的 `Car` 类添加到你的 Xcode 项目中。你可以将创建 `Car` 对象的代码直接添加到你自己的 `main.m` 文件中，亲自测试本方法。构建并运行项目以查看程序结果。如果你检查日志，可以看到 `dealloc` 是在何时执行的。

```
This car is a New Car Name
New Car Name's dealloc is executing
```

如果你注释掉 `main.m` 文件中发送给 `c` 的 `release` 消息，并重新运行你的 Xcode 项目，你将看到 `dealloc` 从未被调用，并且 `c` 中的对象也从未被释放。

## 8.5 使用自动释放



### 问题

你想要从一个自定义类的函数中返回一个通过 `alloc`、`new` 或 `copy` 创建的对象。在将对象返回给调用者之前，你不能释放该对象，因为如果调用者尝试使用它，将会引发内存错误并导致应用崩溃。但如果你不释放它，又会违反第一条内存管理规则，并可能导致内存泄漏。

### 解决方案

在将对象返回给调用者之前，向该对象发送 `autorelease` 消息。这是一种向对象发送延迟 `release` 消息的方式。其思路是：你并不希望对象立即被销毁，因为调用者暂时还需要使用它；但你也希望该对象最终能被释放并销毁。或者，你可能希望调用者通过发送 `retain` 消息来获得对象的所有权。

### 工作原理

`Autorelease` 用于 `Foundation` 对象中那些提供返回对象功能，但未使用常规 `alloc` 和 `init` 消息的函数。例如，`NSDate` 有一个 `date` 函数，它返回一个填充了当前日期和时间的 `NSDate` 对象。虽然你可以自行验证，但在 `NSDate date` 函数的代码中，在将对象返回给你之前，已经向它发送了 `autorelease` 消息。

```
NSDate *today = [NSDate date];
NSLog(@"Today's date is %@", today);
```

你可以使用这个对象，并且无需担心释放它，因为你从未使用 `alloc`、`new` 或 `copy` 函数来创建 `today` 对象。就你的目的而言，其引用计数为零，并且你没有违反第一条内存管理规则。但除非你声明所有权，否则你同样不能假设这个 `today` 对象在未来的某个时刻不会被销毁。

如果你想声明对 `today` 对象的所有权，可以向 `today` 对象发送 `retain` 消息。这会使日期的引用计数变为 1，并且即使对该对象发送了延迟的 `release` 消息，该对象仍会保留在你手中。

例如，如果你想确保 `today` 能留存更长时间，可以这样做：

```
NSDate *today = [NSDate date];
NSLog(@"Today's date is %@", today);
[today retain];

// 执行其他操作...

[today release];
```

现在你知道，可以一直使用 `today` 对象直到你操作完毕。并且，这也符合第二条内存管理规则。

当你创建像 `NSDate date` 这样的函数时，也需要使用 `autorelease`。要了解如何使用 `autorelease`，请为食谱 8.4 中的 `Car` 类添加一个函数。你的目标是编写一个函数，根据调用者提供的名称参数，返回一个 `Car` 对象给调用者。

首先，你需要在 `Car.h` 文件中提供一个前置声明。

```
+(Car *)carWithThisName:(NSString *)carName;
```

你可以看到这是一个类函数，因为函数以加号开头。另外，指定了 `Car` 类作为返回类型，因此你知道该函数将返回一个 `Car` 对象。你还有一个参数，允许调用者为他们将获得的 `Car` 对象指定名称。

当你着手实现时，你的第一反应可能是使用 `alloc` 和 `init` 创建一个新的 `Car` 对象，并在将新对象返回给调用者之前为其赋值 `name` 属性。以下是实现（`Car.m`）中可能出现的内容：

```
+(Car *)carWithThisName:(NSString *)carName{
    Car *car = [[Car alloc] init];
    car.name = carName;

    return car;
}
```

你在这里所做的是创建一个新对象，并在使用 `alloc` 消息时声明了该对象的所有权。然后你没有释放对象就直接返回了，这就违反了第一条内存管理规则。你的调用者无法知道返回的对象引用计数为 1，因此现在你最终会导致内存泄漏。

要解决这个问题，你只需要在将对象返回给调用者之前，发送 `autorelease` 消息。

```
+(Car *)carWithThisName:(NSString *)carName{
    Car *car = [[Car alloc] init];
    car.name = carName;
    [car autorelease];

    return car;
}
```

这会给 `Car` 一个延迟的释放消息，使 `Car` 成为一个临时对象。

这个例子还暗示了另一条内存管理规则。

**规则：** 在函数中，始终在将对象返回给调用者之前，向其发送 `autorelease` 消息。

与此规则相辅相成的还有一条规则。

**规则：** 始终假设从函数返回的对象是自动释放的，因此是临时的，除非你对其发送了 `retain` 消息。

以下是使用 `Car` 对象的方法：

```
Car *tempCar = [Car carWithThisName:@"Temporary Car"];

[tempCar writeOutThisCarsState];
```

相关代码请参见清单 8-8 到 8-12。

### 代码

**清单 8-8.** *Car.h*

```
#import <Foundation/Foundation.h>

@interface Car : NSObject{
@private
    NSString *name_;
}

@property(strong) NSString *name;

+(void)writeDescriptionToLogWithThisDate:(NSDate *)date;

-(void)writeOutThisCarsState;

+(Car *)carWithThisName:(NSString *)carName;

@end
```

**清单 8-9.** *Car.m*

```
#import "Car.h"

@implementation Car

-(void)setName:(NSString *)name{
    [name retain];
    [name_ release];
    name_ = name;
}

-(NSString *) name{
    return name_;
}

+(void)writeDescriptionToLogWithThisDate:(NSDate *)date{
        NSLog(@"Today's date is %@ and this class represents a car", date);
}

-(void)writeOutThisCarsState{
        NSLog(@"This car is a %@", self.name);
}

-(void)dealloc{
    NSLog(@"%@'s dealloc is executing", self.name);
    [super dealloc];
    [name_ release];
    name_ = nil;
}

+(Car *)carWithThisName:(NSString *)carName{
    Car *car = [[Car alloc] init];
    car.name = carName;
    [car autorelease];

    return car;
}

@end
```

**清单 8-10.** *AppDelegate.h*

```
#import <UIKit/UIKit.h>
#import "Car.h"

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@end
```

**清单 8-11.** *AppDelegate.m*

```
#import "AppDelegate.h"

@implementation AppDelegate

@synthesize window = _window;

- (void)dealloc{
    [_window release];
    [super dealloc];
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{

    Car *tempCar = [Car carWithThisName:@"Temporary Car"];

    [tempCar writeOutThisCarsState];

self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    return YES;
}

@end
```

**清单 8-12.** *main.m*

```
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

int main(int argc, char *argv[])
{
    @autoreleasepool {
        NSDate *today = [NSDate date];
        NSLog(@"Today's date is %@", today);
        [today retain];

        // 执行其他操作...

        [today release];

return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```



