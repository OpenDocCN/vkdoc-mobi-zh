# 第 19 章 ■ 观察者模式

以上就是键值观察的基础知识。本章其余部分将深入探讨观察者可用的选项，然后讲解手动实现 `KVO` 以及支持相互关联和复杂属性的更复杂问题。

### 注册键值观察者

当观察者对象注册以观察另一个对象的属性时，需要提供五条信息：

- 源对象
- 观察者
- 源对象属性的键值路径（`key-value path`）
- 一组观察者选项
- 可选的上下文值

要观察的属性的键值路径比乍看起来要灵活得多。

键路径参数接受一个键值编码路径，如第 10 章“键值编码”部分所述。该路径可以描述属性的属性，这巧妙地赋予了 `KVO` 极大的灵活性和能力。考虑清单 19-2 中的代码，它扩展了清单 19-1 中的代码。

**清单 19-2. 观察复杂的键值编码路径**

```objectivec
#import "Chameleon.h"

@interface ReptileZoo : NSObject {
    Chameleon *chameleon;
}
```



```objc
@property (assign) Chameleon *chameleon;

@end

@implementation ReptileZoo

@synthesize chameleon;

@end

…

ReptileZoo *zoo = [ReptileZoo new];

Chameleon *chameleon1 = [Chameleon new];

Chameleon *chameleon2 = [Chameleon new];

Watcher *watcher = [Watcher new];

chameleon1.color = [NSColor blueColor];

chameleon2.color = [NSColor redColor];
```

`zoo.chameleon = chameleon1;`

`[zoo addObserver:watcher forKeyPath:@"chameleon.color" options:NSKeyValueObservingOptionNew context:NULL];`

`chameleon1.color = [NSColor greenColor];`

`zoo.chameleon = chameleon2;`

输出：

```
属性 'chameleon.color' 的对象 '<ReptileZoo: 0x13a0c0>' 已更改：{
    kind = 1;
    new = NSCalibratedRGBColorSpace 0 1 0 1;
}
属性 'chameleon.color' 的对象 '<ReptileZoo: 0x13a0c0>' 已更改：{
    kind = 1;
    new = NSCalibratedRGBColorSpace 1 0 0 1;
}
```

清单 19-2 中的观察者对象被设置为观察 `zoo` 对象的 `chameleon` 属性的 `color` 属性。它接收到了两条属性变更通知。第一条通知是因为 `zoo` 当前 `chameleon` 属性的 `color` 属性被直接设置了新值。第二条通知是因为 `chameleon` 属性被替换为另一个不同的对象，从而间接改变了 `zoo` 的 `chameleon` 属性的 `color` 值。在这两种情况下，观察者都会收到关于受影响完整路径及其新值的通知。

在注册以接收属性变更通知时，通知会通过一个变更字典包含一些关于变更的信息。有四个选项，通过逻辑或运算组合表 19-2 中的任意常量来选定，这些选项控制变更字典中包含哪些信息以及发送通知的顺序。如果您的观察者对所有这些选项都不感兴趣，则在 `options:` 参数中传入 `0`。

**表 19-2.** 键值观察选项

| KVO 选项 | 效果 |
|------------|--------|
| `NSKeyValueObservingOptionNew` | 通知的变更字典包含新值。 |
| `NSKeyValueObservingOptionOld` | 通知的变更字典包含旧值。 |
| `NSKeyValueObservingOptionInitial` | 在 `-addObserver:...` 返回之前立即发送一条包含当前值的通知。 |
| `NSKeyValueObservingOptionPrior` | 每次变更发送两条通知：一条在值变更之前发送的预变更通知，以及其后的一条常规通知。预变更通知的变更字典中会包含一个 `NSKeyValueChangeNotificationIsPriorKey` 键。 |

最后一个参数是一个可选的上下文值。它是一个不受垃圾收集系统管理的 C 指针，因此如果您用它来传递对象指针，请确保该对象不会被过早回收。除此限制之外，它可以包含任何兼容的值，并且会通过该注册触发的所有通知，原样传递回观察者的 `-observeValueForKeyPath:ofObject:change:context:` 方法。

### 处理键值变更通知

注册之后，您的对象将开始以 `-observeValueForKeyPath:ofObject:change:context:` 消息的形式接收键值观察变更通知。该消息包含四个参数：

- 被观察属性的键值路径
- 源对象
- 一个 `change:` 参数，包含一个描述变更的不可变字典
- 一个 `context:` 参数，包含观察者注册时传入 `-addObserver:forKeyPath:options:context:` 的 `context:` 值

如果您的对象注册观察了多个属性，请使用键值路径参数、源对象指针或上下文值来区分属性并分别处理。

变更字典包含关于变更的信息。其内容取决于属性的类型、变更的性质以及您请求包含的信息（由表 19-2 中的选项决定）。可能的字典键以及每个值的说明列于表 19-3 中。



### 表 19-3：变更通知字典键值

| 键 | 值 | 描述 |
| --- | --- | --- |
| `NSKeyValueChangeKindKey` | `NSKeyValueChangeSetting`、`NSKeyValueChangeInsertion`、`NSKeyValueChangeRemoval` 或 `NSKeyValueChangeReplacement` | 描述属性的变化方式。如果属性是简单属性（如整数或对象），则此值为 `NSKeyValueChangeSetting`。其他三个值描述了对集合属性的更改。 |
| `NSKeyValueChangeNewKey` | 值对象 | 包含新的属性值，或插入或替换值的数组。仅当请求了 `NSKeyValueObservingOptionNew` 选项时才包含此项。 |
| `NSKeyValueChangeOldKey` | 值对象 | 先前的属性值，或移除或替换值的数组。仅当请求了 `NSKeyValueObservingOptionOld` 选项时才包含此项。 |
| `NSKeyValueChangeNotificationIsPriorKey` | `YES` | 如果这是通过 `NSKeyValueObservingOptionPrior` 观察选项请求的变更前通知，则包含此项。 |
| `NSKeyValueChangeIndexesKey` | `NSIndexSet` | 对于数组集合属性的更改，此 `NSIndexSet` 值包含受插入、移除或替换影响的索引。 |

当观察一个原始值或对象指针类型的属性变化时，变更种类始终是 `NSKeyValueChangeSetting`，并且字典中可能会酌情包含旧值和新值。

当观察的属性是一个对多对象属性时，变更信息会稍微复杂一些。变更被描述为集合中一个或多个对象的插入、移除或替换。`NSKeyValueChangeIndexesKey` 值列出了集合中受变更影响的索引。`NSKeyValueChangeNewKey` 和 `NSKeyValueChangeOldKey` 值包含一个紧凑的数组，其中包含了在 `NSKeyValueChangeIndexesKey` 集合中相应索引处插入、移除或替换（视情况而定）的对象。例如，如果从索引 3 和 5 的属性中移除了两个对象，那么 `NSKeyValueChangeIndexesKey` 将包含集合 {3, 5}，`NSKeyValueChangeOldKey` 将包含在索引 0 处索引 3 被移除的对象以及在索引 1 处索引 5 被移除的对象，而 `NSKeyValueChangeNewKey` 值将为空，因为没有添加新对象。

> **注意：** 观察对多属性要求被观察对象实现第 10 章“设计符合 KVC 的类”一节中描述的可变数组属性访问器方法。常规的 `NSMutableArray` 类并未实现这些方法。如果你的对象有一个 `NSMutableArray *array` 属性，并且你观察该对象的 `@"array"` 属性，那么当数组对象被设置时，你的观察者会收到通知，但当对象被添加或从该数组中移除时，则不会收到通知。像 `NSArrayController` 这样的类确实实现了此协议，并且被设计为可观察的。

### 注销观察者

当你的观察者想要停止接收属性变更通知时，请向被观察对象发送一条 `-removeObserver:forKeyPath:` 消息，并传入与添加观察者时相同的键值路径。

对象对其观察者持有弱引用，因此在观察者可以被回收之前，不必移除观察者。然而，如果存在观察者不再需要接收属性变更通知的逻辑节点，它应该将自己移除。另一方面，仅仅是作为某个对象的观察者，并不会阻止观察者本身被回收；观察者需要至少有一个强引用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 使你的类符合 KVO 标准

当一个对象属性符合键值观察标准时，意味着可以使用键值观察来可靠地观察该属性。要符合 KVO 标准，对象的属性必须：

- 符合键值编码标准
- 在被设置时发送键值观察通知



如果您为标准属性实现 getter 和 setter 方法，并通过发送 setter 消息来更改该属性，那么您的属性将 100% 符合 KVO 规范。

然而，在某些情况下，您的属性将不符合 KVO 规范。这些情况要求您直接向类中添加 KVO 支持代码——假设您希望使该属性可观察。别担心，代码并不复杂。添加什么代码、在哪里添加以及在什么情况下添加，将在接下来的几个部分中描述。以下是属性不符合 KVO 规范的主要原因：

- 该属性在其 setter 方法之外使用直接赋值（例如，`object->property = 1`）进行设置。
- 该属性是其他属性的组合。一个典型的例子是只读的 `fullName` 属性，它由 `firstName` 和 `lastName` 属性拼接而成。
- 该属性因其他状态变化而改变。例如，一个定义为 `return ([lock condition] != RUNNING)` 的 `isFinished` 属性。

直接赋值绕过了 KVO 拦截对象 setter 方法并注入必要通知的能力。要解决此问题，请手动发送 KVO 通知，或在更改属性值时重写代码以使用该属性的 setter 方法。

通过将依赖关系告知 Key-Value Observing 框架，可以使依赖属性符合 KVO 规范。KVO 提供了特殊方法，您可以在类中描述依赖键。一旦实现，KVO 将知道更改 `firstName` 或 `lastName` 属性也会更改 `fullName` 属性，并发送所有预期的通知。

因其他状态变化而自发更改的属性通常需要结合手动 KVO 通知或依赖属性来解决。

除了仅仅作为行为良好的 KVO 参与者外，还有其他原因可能促使您直接向类中添加 KVO 支持代码。其中一个原因是性能。Key-Value Observing 通知有点“笨”。每当调用 setter 方法时，KVO 都会通知其观察者——即使新值与旧值相同。如果这导致性能或递归问题，您可以实现自己的 KVO 通知，仅在您认为适当时才发送。

而且，毫无疑问，总会出现上述未描述的情况，只能通过实现您自己的 KVO 支持来解决。这些特殊情况不仅涉及向类中添加手动 KVO 通知代码，您可能还需要告诉 KVO 框架不要执行其正常服务，否则两者会互相干扰。最后，需要注意的是，这些解决方案经常重叠；解决特定的 KVO 问题通常有多种方法。在决定最适合您情况的解决方案之前，请阅读接下来的几个部分。

### 发送手动 KVO 通知

每当可观察属性发生更改时，您需要确保发送正确的 KVO 通知消息。在符合 KVC 规范的 setter 方法中，这会自动发生。在以下情况下，您可能需要手动发送 KVO 通知：

- 该属性是只读的，并且没有 setter 方法。
- 该属性在 setter 方法之外使用直接赋值进行设置。
- 该属性是一个合成值，从其他属性值计算得出。

最直接的方法是在可能修改属性的代码之前和之后，手动触发所需的通知，发送 `-[NSObject willChangeValueForKey:]` 和 `-[NSObject didChangeValueForKey:]` 消息。这些消息通知 KVO 框架即将发生某些可能导致属性更改的事情。KVO 框架负责向任何观察者发送适当的通知。以下代码清单 19-3 在一个简单的 `Budget` 类中演示了这一点。



`列表 19-3`。手动触发 KVO 通知

```objc
@interface Budget : NSObject {

double budget;

double plannedExpense;

}

@property double budget;

@property (readonly) double plannedExpense;

- (void)addExpenditure:(double)amount;

@end

@implementation Budget

@synthesize budget, plannedExpense;

- (void)addExpenditure:(double)amount

{

[self willChangeValueForKey:@"plannedExpense"]; plannedExpense += amount;

[self didChangeValueForKey:@"plannedExpense"];

}

@end
```

`Budget` 类有一个存储在变量中的 `plannedExpense` 属性。这是一个没有设置方法的只读属性。`-addExpenditure:` 方法在增加金额时会改变该属性。包围该变更的 `-willChangeValueForKey:` 和 `-didChangeValueForKey:` 消息会将变更通知给键值观察框架，并允许其向观察者发送属性通知。

---

## 观察者模式

**注意** 对于给定的键，每条 `-willChangeValueForKey:` 消息必须与同一条键的相应 `-didChangeValueForKey:` 消息成对出现。

如果 `-addExpenditure:` 方法修改了多个属性，它就会为每个属性发送一对 `-willChangeValueForKey:`/`-didChangeValueForKey:` 消息。这些消息可以嵌套：代码可以发送三条 `-willChange...` 消息，修改三个属性，然后再发送三条 `-didChange...` 消息。

如果要修改的属性是一个对多关系的数组属性，请改用 `-willChange:valuesAtIndexes:forKey:` 和 `-didChange:valuesAtIndexes:forKey:` 消息。

### 创建属性依赖关系

另一个常见问题是当另一个属性发生变化时，某个属性也会隐式地随之变化。这被称为依赖属性。为 `Budget` 类添加一个 `remainingBudget` 属性会创建一个同时受 `budget` 或 `plannedExpense` 属性变更影响的依赖属性。键值观察框架定义了一个非正式协议，允许你的类描述其依赖属性。修改后的 `Budget` 类如 `列表 19-4` 所示。

`列表 19-4`。定义依赖属性

```objc
@interface Budget : NSObject {

double budget;

double plannedExpense;

}

@property double budget;

@property (readonly) double plannedExpense;

@property (readonly) double remainingBudget;

- (void)addExpenditure:(double)amount;

@end

@implementation Budget

@synthesize budget, plannedExpense;

- (void)addExpenditure:(double)amount

{

[self willChangeValueForKey:@"plannedExpense"];

plannedExpense += amount;

[self didChangeValueForKey:@"plannedExpense"];

}

+ (NSSet*)keyPathsForValuesAffectingRemainingBudget

{

return [NSSet setWithObjects:@"budget", @"plannedExpense", nil];

}

- (double)remainingBudget

{

// 剩余金额或 0.0

return MAX(0,budget-plannedExpense);

}

@end
```

对于每个被观察的属性，键值观察框架会查找一个名为 `+keyPathsForValuesAffectingProperty` 的类方法；将方法名中的 “`Property`” 部分替换为依赖属性的名称。该方法返回一个该属性所依赖的属性名称集合。每当这些其他属性中的任何一个发生变化时，KVO 也会为该依赖属性发送一个变更通知。为每个依赖属性实现一个这样的类方法。

**注意** 使用上一节演示的手动 KVO 通知也可以实现相同的解决方案。你需要修改 `-addExpenditure:` 方法，使其为 `@"remainingBudget"` 键路径发送额外的 `-willChangeValueForKey:` 和 `-didChangeValueForKey:` 消息，然后在手写的 `-setBudget:` 方法中执行相同的操作。依赖属性协议的优势在于它不需要修改其他属性的设置方法，也不需要添加更多的手动 KVO 通知。这在定义依赖于其超类属性的子类中特别方便。它还能避免 KVO 代码使实现变得杂乱。

实际上有三种方法可以向键值观察框架传达依赖属性：

-   实现一个按属性的 `+keyPathsForValuesAffectingProperty` 类方法，如 `列表 19-4` 所示。
-   重写单个 `+keyPathsForValuesAffectingValueForKey:` 类方法，并为请求的键返回依赖集。该方法的基类实现使用键路径来查找并调用选项 1 中实现的方法名。
-   在类中任何对象被观察之前的某个时刻（通常是在类的 `+initialization` 方法中），为类中实现的每个依赖键发送一条 `+setKeys:triggerChangeNotificationsForDependentKey:` 消息。

对于每个依赖属性，仅实现上述解决方案之一。在实现前两个解决方案中的任何一个时，不要忘记在返回的集合中包含由超类定义的任何依赖键。

前两个解决方案仅适用于在 Mac OS X 10.5 或更高版本上运行的简单（对一）属性。如果你需要建立一个对多关系的依赖属性，或者目标平台是 Mac OS X 10.4 或更早版本，请使用最后一个解决方案。如果你的目标是 Mac OS X 10.5 或更高版本（其中 `+setKey:…` 方法已弃用），请参阅“键值观察编程指南”¹ 中的“注册依赖键”部分，以了解多种可能的解决方案。

### 重写键值观察

要在符合 KVC 的设置方法中实现手动 KVO 通知，你必须禁用键值观察框架将执行的常规变更通知。否则，KVO 和你的代码都会发送通知，导致重复的通知或顺序错乱的通知。

这样做的一个非常审慎的原因是优化设置方法，使其仅在值实际发生变更时才通知观察者。这可以带来性能优势，并解决递归或循环更新问题。在 `列表 19-5` 中，类的 `budget` 属性设置器被重新实现，以便仅在 `budget` 值实际发生变化时才发送 KVO 通知。

`列表 19-5`。优化 KVO 通知

```objc
@implementation Budget

+ (BOOL)automaticallyNotifiesObserversForKey:(NSString*)key

{

if ([key isEqualToString:@"budget"])

return NO;

return [super automaticallyNotifiesObserversForKey:key];

}

- (double)budget

{

return budget;

}

- (void)setBudget:(double)value

{

if (budget!=value) {

[self willChangeValueForKey:@"budget"]; budget = value;

[self didChangeValueForKey:@"budget"];

}

}
```

为防止 KVO 框架重写 `-setBudget:` 方法并发送其自身的通知，`+automaticallyNotifiesObserversForKey:` 类方法被重写。对于生成自身通知的可观察属性，该方法应返回 `NO`。基类实现为所有键返回 `YES`。

然后，`-setBudget:` 方法被重写，使其仅在新预算值与先前值不同时才发送 KVO 通知。这种优化可以产生连锁效应。例如，既然 `remainingBudget` 属性依赖于 `budget` 属性，那么不发送多余的预算通知实际上避免了两个多余的通知。

### 优化键值观察

¹ Apple Inc., “键值观察编程指南”, [`developer.apple.com/documentation/Cocoa/Conceptual/KeyValueObserving/`](http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueObserving/), 2009.




除了上一节中描述的消除冗余通知外，你的对象还可以通过另一种方式帮助键值观察框架。对于被大量观察的对象，你可以提供一个位置来存储关于该对象的观察信息，从而优化 KVO 性能。虽然 KVO 可以使用 `isa swizzling` 来改变对象的类，但它无法改变对象的结构或在其中存储任何新值。相反，KVO 会将对象的观察信息保存在一个全局集合中，每次属性发生变化时都必须查询该集合。

你可以通过为 KVO 提供一个 `void *observationInfo` 属性来减少这种开销，如 代码清单 19-6 所示。键值观察框架将使用此属性将对象的观察信息直接存储在对象内部。

**代码清单 19-6.** 提供本地键值观察信息存储

```
@interface KVOFriendly : NSObject {
@private
    void* observationInfo;
}
@property (assign) void *observationInfo;
@end

@implementation KVOFriendly
@synthesize observationInfo;
@end
```

### 摘要

虽然在概念上与提供者/订阅者模式相似，但观察者模式提供了一套截然不同的解决方案。其主要优势在于，对于被观察对象而言，观察和通知在很大程度上是透明的，并且通常无需预先设计即可提供所需的通知。虽然基本属性会自动兼容键值观察，但某些属性可能需要添加一些额外代码才能完全支持 KVO。

[www.it-ebooks.info](http://www.it-ebooks.info/)

---

