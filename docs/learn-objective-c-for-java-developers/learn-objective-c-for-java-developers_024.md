# 排版后内容

```
for (Meal *meal in [self meals]) {
    for (Ingredient *ingredient in [[meal recipe] ingredients])
        [budget planExpenditure:[ingredient cost]];
    if ([budget isOverBudget])
        affordableGroceryList = nil;
    [mealList addObject:meal];
    [affordableGroceryList addObjectsFromArray:[[meal recipe] ingredients]];
}
@end
```

Java 方法使用传统条件语句来分支处理各种情况。而 Objective-C 方法则很大程度上依赖于对象的存在与否来决定其行为。如果 `budget` 对象是 `nil`，则不会发送 `-planExpenditure:` 消息，且 `-isOverBudget` 消息始终返回 `NO`。如果某餐的 `recipe` 属性为 `nil`，则不处理任何食材。如果超出预算，则将 `affordableGroceryList` 指针设置为 `nil`，并且 `shoppingList` 将停止接收 `-addObjectsFromArray:` 消息。

要开始使用 `nil` 进行设计，请遵循以下三个原则：属性访问器、缺席行为和与“无”的一致性。

#### 属性访问器

您可能已经遵循 Java 的实践，为所有类属性编写访问器方法。Objective-C 只是为您提供了继续进行这项实践的一个理由。

由于 `nil` 对象始终返回 `nil` 或 `0`，因此所有获取或设置表 7-1 中某一类型的属性访问器方法都可以安全地用于 `nil` 接收器。在代码清单 7-4 中，无论 `meal` 指向的是 `Meal` 对象还是 `nil`，表达式 `[meal recipe]` 都可以被调用。相比之下，如果 `meal` 是 `nil`，表达式 `meal->recipe` 将导致应用程序崩溃。同样地，如果 `meal` 是 `nil`，语句 `[meal setRecipe:recipe]` 将不执行任何操作。

## 缺席行为

围绕对象的存在与否来设计可选行为。当对象存在时，对象执行消息；当对象不存在时，则什么都不发生。简单的例子有：

*   `Logger` 对象：发送给日志记录器的消息会被记录，如果日志记录器为 `nil`，则忽略。
*   `Listener` 对象：只有在建立了监听器后，才会发送状态和更新消息。将监听器设置为 `nil` 将抑制更新。
*   `Delegate` 对象：如果设置了委托，则查询委托；否则使用默认答案。这是缺席行为和与“无”的一致性设计的结合，将在下文中讨论。

代码清单 7-5 展示了一个简单的 FIFO 栈类，它可以以线程安全的方式运行，但仅在应用程序需要它线程安全时才如此。

**代码清单 7-5.** 缺席对象行为

```
@interface AutoSafeFIFO : NSObject {
    NSMutableArray *stack;
@private
    NSLock *lock;
}
- (void)push:(id)object;
- (id)pop;
- (BOOL)hasObjects;
- (void)makeThreadSafe;
@end

@implementation AutoSafeFIFO

- (id)init
{
    self = [super init];
    if (self != nil) {
        stack = [NSMutableArray new];
    }
    return self;
}

- (void)push:(id)object
{
    [lock lock];
    [stack addObject:object];
    [lock unlock];
}

- (id)pop
{
    id object = nil;
    [lock lock];
    if ([stack count] != 0) {
        object = [stack objectAtIndex:0];
        [stack removeObjectAtIndex:0];
    }
    [lock unlock];
    return object;
}

- (BOOL)hasObjects
{
    [lock lock];
    BOOL answer = ([stack count] != 0);
    [lock unlock];
    return answer;
}

- (void)makeThreadSafe
{
    lock = [NSLock new];
}

@end
```

`lock` 对象初始为 `nil`，因此所有发送给 `lock` 和 `unlock` 的消息都不会执行任何操作。这提供了最佳性能，但牺牲了线程安全。为了在多线程环境中安全地运行，`-makeThreadSafe` 方法将 `lock` 属性设置为一个真正的 `NSLock` 对象。一旦设置，`lock` 和 `unlock` 消息现在会发送给 `NSLock` 对象，从而提供所需的线程同步。

## 与“无”的一致性

设计您的类属性，使得 `nil` 或 `0` 的值与“无”对象的概念保持一致。通常，这意味着定义表达正面属性（而非负面属性）的属性——这通常是一个好习惯。在代码清单 7-5 中，`AutoSafeFIFO` 类定义了 `-(BOOL)hasObjects`。您可能想定义一个 `-(BOOL)isEmpty` 方法。但如果接收器是 `nil`，`isEmpty` 将返回 `NO`，这暗示着 `nil` 对象拥有对象，而显然它并没有。

### 没有免费的午餐

Objective-C 对 `nil` 接收器的处理可以使您的代码更简单、更安全、更优雅。然而，这并不意味着 `nil` 在任何对象指针所在的地方都是普遍可接受的。正如您已经看到的，尝试使用 `nil` 对象指针访问成员值将导致程序突然终止。大多数接受对象指针的方法参数通常期望一个对象，而不是 `nil`。例外情况通常会在文档中说明。

例如，大多数 Cocoa 集合类——就像 Java 一样——不允许将 `nil` 对象作为值存储或用作键。因此，虽然向 `nil` 接收器发送 `-addObject:` 消息是完全安全的，但语句 `[array addObject:nil]` 会抛出一个运行时异常（当然，前提是 `array` 不是 `nil`）。

### 总结

以 Java 背景出身，我可以告诉您，拥抱 `nil` 接收器需要一些时间来适应。有一种潜意识的、近乎本能的趋势，即在设计的每个步骤以及编写代码时都避免空引用。学习如何利用 `nil` 对象为您带来好处需要一些练习，但最终是值得的。

---

