# 类的方法

一旦你为类定义了属性，你可能会想使用*点语法*来访问它们。这种语法让你无需使用方括号即可访问实例变量。终于，我们这些 C++ 和 Java 程序员可以松一口气了！对于许多开发者来说，这种代码会更具可读性：

```
AwesomeStringMaker *myAwesomeStringMaker = [[[AwesomeStringMaker alloc] init] autorelease];

// 使用点语法设置属性的值...
myAwesomeStringMaker.exclamationCount = [NSNumber numberWithFloat:8.0];
myAwesomeStringMaker.originalString = @"typing power";

NSString *myAwesomeString = [myAwesomeStringMaker awesomeString];

// 或使用点语法读取属性
if ([myAwesomeStringMaker.exclamationCount integerValue] < 4) {
    // 这还不够棒
    myAwesomeStringMaker.exclamationCount = [NSNumber numberWithFloat:8.0];
}
```

等号左边的属性将使用 setter 方法，而等号右边的同一个属性将使用 getter 方法。

## 类的方法

此前，你可能会对这段代码感到有些困惑：

```
[NSString stringWithUTF8String:"Even"]
```

就在我们讨论向对象实例发送消息时，现在你却没有使用对象实例。
这条消息到底发到哪里去了？

要回答这个问题，我们先看一下 `NSString` 接口中的定义：

```
+ (id)stringWithUTF8String:(const char *)bytes;
```

没错，这又是 Objective-C 带来的一个古怪字符。这次是个加号（`+`）。

在第 37 页中，你看到过方法定义以减号开头。
但那些只是*实例*方法。*类*方法是另一种类型。

对于实例方法，你需要一个引用对象实例的变量才能发送消息。有时这会在你的类设计中造成限制：你希望在不依赖对象实例的情况下也能调用某个方法。类方法正是 Objective-C 解决此问题的方案。使用这些方法，你可以直接向类发送消息，而不需要实际的对象。

这些类方法通常用于创建新的对象实例。`+stringWithUTF8String:` 方法就是这样一个例子；向类发送消息让它返回一个供你在代码中使用的新对象。

## 初始化对象

> **注意：** 你可能熟悉使用*工厂*对象。这正是向类发送消息所做的事情。`+stringWithUTF8String:` 消息充当了 `NSString` 新实例的工厂。

那么，怎样才能让你的 `AwesomeStringMaker` 类更上一层楼呢？一个返回 `mostAwesomeString` 的类方法：

```
@interface AwesomeStringMaker : NSObject
. . .
+ (NSString *)mostAwesomeString;
@end
```

你的实现代码会是这样：

```
@implementation AwesomeStringMaker
. . .
+ (NSString *)mostAwesomeString {
    return @"CHOCKLOCK!!!!!!!!";
}
@end
```

现在，当你需要让你的应用更加出色时，甚至无需思考：

```
[label setTitle:[AwesomeStringMaker mostAwesomeString]];
```

## 初始化对象

你的 `AwesomeStringMaker` 类有一个 bug。这很难想象，不是吗？

```
AwesomeStringMaker *myAwesomeStringMaker = [[[AwesomeStringMaker alloc] init] autorelease];
myAwesomeStringMaker.originalString = @"typing power";
NSString *myAwesomeString = [myAwesomeStringMaker awesomeString];
```

`myAwesomeString` 的值是“TYPING POWER”。它缺少了一些令人惊叹的感叹号！！！！！这是因为当创建一个新对象实例时，所有实例数据都会被清空。指针被设置为 `nil` 值，数字被设置为零。这意味着你的`exclamationCount` 实例变量为零。而零个感叹号是无法让人感到震撼的。

这种情况揭示了一个更大的问题：你的对象通常需要在发送消息之前进行设置。在 Objective-C 中，有一个专门用于此目的的方法：

```
- (id)init {
    if (self = [super init]) {
        // 既然能用很多，为什么只用一点？
        exclamationCount = [[NSNumber numberWithInt:8] retain];
        originalString = [@"" copy];
    }
    return self;
}
```

这段代码看起来可能有些复杂，但它做的事情既简单又优雅：每个对象在内存中分配之后，新的实例都会收到 `-init` 消息。你可以选择忽略这条消息，但更可能的情况是你不会忽略。

> **注意：** 你可能想知道为什么每个对象都会收到 `-init` 消息。这是因为默认实现位于 `NSObject` 中，而每个对象都是这个根类的后代。如果你没有实现这个方法，它将被超类（包括 `NSObject`）处理。

当你的对象收到该消息时，它会将向父对象（由隐式变量 `super` 表示）发送 `-init` 消息的结果赋值给变量 `self`。这又是怎么回事呢？

就像你的类需要进行一些初始化工作一样，你的父类也可能需要做一些。你并不确切知道那些设置是什么，但现在是你与 `NSObject` 之间的所有类进行初始化的唯一机会。

在给了所有其他类完成设置的机会后，你检查赋值给 `self` 的结果。有可能某个超类无法初始化自身，从而返回了 `nil`。如果是这种情况，你也会跳过自己的初始化并返回 `nil`。

假设超类一切顺利，你将有机会把 `exclamationCount` 设置为惊人的 8 个字符。

### 释放位置

你的代码中仍然存在一个相当严重的 bug。你费尽心思地进行了保留、复制和释放访问器的工作。但当整个对象被释放时会发生什么？除非你调用了每个访问器并将值设置为 `nil`，否则实例变量使用的内存将永远不会被释放。

在实现类时，你有责任在类的对象被释放时清理自己。要做到这一点，你需要使用 `-dealloc` 方法。就像 `-init` 让你进行设置一样，`-dealloc` 方法则让你进行拆除工作。以下是你的 `AwesomeStringMaker` 类中该方法应有的样子：

```
- (void)dealloc {
    [exclamationCount release];
    [originalString release];
    [super dealloc];
}
```

首先，释放你的两个实例变量，以便可以回收它们占用的内存。
然后，将 `-dealloc` 消息转发给超类，让它执行自己的清理工作。

### 手动重写

你可能已经注意到，`-init` 和 `-dealloc` 从未在你的 `@interface` 中定义过。这是因为没有必要；作为 `NSObject` 的后代，这个方法已经被声明过了。你自己对这两个方法的实现只是“重写”了在超类中定义的那些方法。



