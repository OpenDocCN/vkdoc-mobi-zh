# 第 4 章：在应用中保存内容

无论你的 Cocoa Touch 应用有多么出色，用户都不可能一直使用它。他们会接到电话，切换到其他应用，下载新应用，甚至更换新设备。当他们回到你的应用时，他们希望——并且期待——所有内容都和他们上次使用时完全一样。他们的数据应该还在（并且未发生变化），甚至用户界面也应该和他们上次打开应用时一模一样。其中一部分功能得益于应用在未打开时仍保留在内存中，但当 iOS 将其从内存中移除以回收空间时，你需要准备好按需重新创建这些数据。在本章中，我们将讨论如何将应用的数据持久化到磁盘，以便在多次启动之间保存数据。不过，由于凡事都要循序渐进，我们先来讨论如何在应用内部移动数据；如果你无法在应用内部移动数据，那么将其移出应用也会很困难。在讨论了如何在应用内部移动数据之后，我们将讨论如何将这些数据持久化到磁盘，以便在多次启动之间可用。

## 在应用内移动数据

到目前为止，你在移动数据方面的经验还非常基础。在示例应用 MyStuff 中创建物品列表时，你传递的唯一数据就是用户点击以显示详情视图控制器的那件物品。由于两个视图控制器之间存在直接的父子关系，传递指向该物品的指针简单又容易，但情况并非总是如此简单。通常情况下，你无法在两个对象之间建立直接链接，却需要在它们之间传递数据。最简单的方法就是你在 MyStuff 中所做的：直接传递对象，并在反向传递时使用委托。在本节中，我们将讨论如何通过这种方法以及其它方法在应用的不同部分之间传递数据。

### 委托链

当你将指向某个对象的指针从父视图控制器传递给其子视图控制器，然后通过委托协议将其传回给父视图控制器时，数据的流向是清晰且受控的。然而，如果你需要在这个流程中增加更多步骤，事情就会变得复杂。假设你的详情视图控制器需要呈现另一个二级详情视图控制器来修改对象的某些特定细节。解决方案是将指向该对象的指针从详情视图控制器传递到一个新的子视图控制器。一旦该视图控制器完成工作，它会通过一条委托消息通知其父视图控制器，而父视图控制器又会以同样的方式通知其上一级父视图控制器。你可能已经看出，这种模式可以无限延伸，形成一个由视图控制器组成的、将对象传递给子视图控制器并接收回传消息的连续链条。这种方法可行，但并不是最优雅的解决方案，尤其对于复杂的视图控制器层级而言；而且它仅在两个视图控制器之间存在直接连接（例如这种父子关系）时才能工作。

### 键值观察

当我们在 MyStuff 中为详情视图控制器编写委托协议时，目的是告诉父视图控制器我们已完成对对象的编辑，从而允许我们更新用户界面并显示新的值。对于我们的父视图控制器（它只是一个对象列表）来说，这些对象在哪里被修改并不重要；我们只是希望始终呈现尽可能最新的信息。例如，你以后可能会实现一个跨设备同步对象的网站，然后在后台从该网站进行更新。在这些情况下，由于我们关心对象值的变化，我们可以使用一种名为键值观察（KVO）的 Objective-C 范式，在值发生变化时接收通知。

键值观察是在根类 `NSObject` 中实现的，这意味着它几乎适用于你将在 Apple 平台的 Objective-C 代码中使用的所有对象，包括 iOS 上的 Cocoa Touch。它允许我们观察对象的值，并在该值发生变化时接收通知。这个值通常是对象的一个属性，不过稍后我们还会探讨其他用途。

#### 使用 KVO

要注册一个属性变化时的通知，请在你想观察的对象上调用 `addObserver:forKeyPath:options:context:` 方法：

```
[someObject addObserver:self
             forKeyPath:@"propertyName"
                options:NSKeyValueObservingOptionNew
                context:NULL];
```

这会将 `self` 添加为 `someObject` 的观察者，当其名为 `propertyName` 的属性发生变化时触发。`options` 参数是一个位掩码，用于指定如何



你希望收到通知。传递 `NSKeyValueObservingOptionNew` 会在值变化时，通知中包含新值。`context` 参数允许你为自定义上下文指定一个 `void` 指针，但在实践中很少使用。当你使用 ARC 编程时，编译器禁止你将对象用作上下文指针，而在 ARC 普及之前，这曾是用途之一。当值发生变化时，它会调用以下方法：

```
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary *)change
                       context:(void *)context
{
    // 在此处编写响应变化的代码
}
```

你需要为任何作为观察者的类实现此方法。在我们的示例中，`keyPath` 参数将是 `propertyName`，`object` 参数将是 `someObject`，但你应该始终检查这些值，以确保你观察到了正确的变化。要观察 `self` 上的 `flag` 属性值，你首先需要调用此方法：

```
[self addObserver:self
       forKeyPath:@"flag"
          options:0
          context:NULL];
```

为了确保你对正确的变化做出响应，当观察到变化时，应验证这些值：

```
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary *)change
                       context:(void *)context
{
    if (object == self) {
        if ([keyPath isEqualToString:@"flag"]) {
            // 在此处响应变化
        }
    }
}
```

两个连续的 `if` 语句有助于保证你的应用在未来的兼容性，因为 `observeValueForKeyPath:ofObject:change:context:` 方法可能会被多个不同的变化调用。

`change` 字典将根据我们在添加观察者时指定的选项包含不同的值；对于 `NSKeyValueObservingOptionNew`，新值将在字典中对应键 `NSKeyValueChangeNewKey`。在此方法中，你应该适当地响应变化，无论是更新 UI、保存内容还是触发进一步的更改。

**注意：** 非常重要的一点是，在完成观察后，务必通过调用对象上的 `removeObserver:forKeyPath:` 来移除观察者。如果你指定了上下文指针，可以使用 `removeObserver:forKeyPath:context:` 方法仅移除该上下文指针对应的观察者。当你不再需要调用观察者时，或者对于需要在生命周期内始终接收变化通知的对象，应在 `dealloc` 方法中执行此操作。否则，你可能会在对象被释放后仍收到通知，从而导致应用崩溃。

## KVO 的工作原理

在日常编程中开始使用 KVO 时，你会很快注意到：如果你创建了自己的对象，并对其属性使用 KVO，只要通过合成的 setter 方法设置属性，就无需编写任何代码来获取这些通知。这是如何实现的？它利用了 Objective-C 的动态特性。由于 Objective-C 运行时允许在程序运行时创建类，这正是它所做的。运行时会创建你所观察的类的一个子类，并重写你所观察属性的 setter 方法。该 setter 方法会调用原始的 setter 方法，然后发送你的通知。一旦这个新类被创建，运行时会执行一个巧妙的操作：它将所观察对象的类指针更改为这个新类。我不建议你自己这样做，但在 KVO 的情况下，这是完全安全的。

## 手动 KVO 实现

到目前为止我们介绍的 KVO 功能，对于一次更改单个属性来说效果极佳。然而，更复杂的对象需要特殊处理。



一个常见的情况是，自定义 setter 有其他副作用时，KVO 会力不从心。例如，一个代表房屋贷款的类可能会有一个 `principal` 属性、一个 `interestRate` 属性，以及一个只读的 `monthlyPayment` 属性。当你修改 `interestRate` 属性时，`monthlyPayment` 属性需要重新计算。在这种情况下，你可以调用另一个属性的 setter，从而触发另一个 KVO 通知，但如果 `monthlyPayment` 不是一个可写属性，我们可以手动触发通知：

```
- (void)setInterestRate:(float)newInterestRate
{
    [self willChangeValueForKey:@"interestRate"];
    interestRate = newInterestRate;
    [self didChangeValueForKey:@"interestRate"];
    [self willChangeValueForKey:@"monthlyPayment"];
    monthlyPayment = MonthlyPaymentForInterestRate(interestRate);
    [self didChangeValueForKey:@"monthlyPayment"];
}
```

如你所见，每次调用 `willChangeValueForKey:` 都需要与对同一键路径参数调用的 `didChangeValueForKey:` 成对出现。如果你实现了这一点，还应重写类方法 `automaticallyNotifiesObserversForKey:`，为你手动发送通知的键路径返回 `NO`。假设你有一个包含 `subtotal` 和 `taxRate` 两个属性的类，它们用于计算第三个只读属性 `totalDue`。你应首先实现 `automaticallyNotifiesObserversForKey:` 以避免自动返回 `totalDue` 的通知：

```
+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key
{
    if ([key isEqualToString:@"totalDue"]) {
        return NO;
    }
    return [super automaticallyNotifiesObserversForKey:key];
}
```

接下来，当你设置 `subtotal` 或 `taxRate` 值时，你可以手动通知观察者 `totalDue` 的变化：

```
[self willChangeValueForKey:@"totalDue"];
_totalDue = [self subtotal] + ([self subtotal] * [self taxRate]);
[self didChangeValueForKey:@"totalDue"];
```

使用此方法，你可以控制何时调用观察方法，并为数据提供自定义逻辑。

## KVO 实战

现在，让我们将学到的 KVO 知识应用到 MyStuff 应用中。从 `PossessionListViewController.m` 中删除以下代码行：

```
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [[self tableView] reloadData];
}
```

我们将使用 KVO 自动更新表格单元格的内容，而不是每次视图出现时重新加载整个表格视图的内容。这将导致新项目不会立即显示，但我们将在本章后面修复这个问题。在 Xcode 中，创建一个新文件（文件 ➤ 新建 ➤ 新建文件...），在左侧栏的 Cocoa Touch 中选择 Objective-C 类。点击下一步，将类命名为 `PossessionListTableViewCell`，并使其成为 `UITableViewCell` 的子类。保存到磁盘，Xcode 会将其添加到项目中。打开头文件（`PossessionListTableViewCell.h`），并添加以下加粗显示的代码行：

```objc
#import <UIKit/UIKit.h>

@class Possession;

@interface PossessionListTableViewCell : UITableViewCell

@property (strong, nonatomic) Possession *possession;

@end
```

切换到头文件（`Possession.h`），同时按下 ⌘、Control 和上箭头键（或者在 Xcode 的文件浏览器中选择它），并删除 Xcode 模板创建的方法。添加以下加粗显示的代码（稍后我们会逐步讲解）：

```objc
#import "PossessionListTableViewCell.h"
#import "Possession.h"

static NSString * const kPossessionNameKeyPath = @"name";
static NSString * const kPossessionValueKeyPath = @"value";

@implementation PossessionListTableViewCell {
    BOOL isObservingPossession;
}

@synthesize possession = _possession;

- (void)dealloc
{
    if (isObservingPossession == YES) {
        [_possession removeObserver:self
                        forKeyPath:kPossessionNameKeyPath];
        [_possession removeObserver:self
                        forKeyPath:kPossessionValueKeyPath];
        isObservingPossession = NO;
    }
}

- (void)setPossession:(Possession *)possession
{
```



```objective-c
if (isObservingPossession == YES) {
    [_possession removeObserver:self
                    forKeyPath:kPossessionNameKeyPath];
    [_possession removeObserver:self
                    forKeyPath:kPossessionValueKeyPath];
    isObservingPossession = NO;
}

_possession = possession;

if (_possession != nil) {
    [_possession addObserver:self
                 forKeyPath:kPossessionNameKeyPath
                    options:(NSKeyValueObservingOptionInitial |
                             NSKeyValueObservingOptionNew)
                    context:NULL];
    [_possession addObserver:self
                 forKeyPath:kPossessionValueKeyPath
                    options:(NSKeyValueObservingOptionInitial |
                             NSKeyValueObservingOptionNew)
                    context:NULL];
    isObservingPossession = YES;
}
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

