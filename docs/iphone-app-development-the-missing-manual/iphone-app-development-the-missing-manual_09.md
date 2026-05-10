# 当编译器警告“方法中传递参数”时

当编译器发出“方法中传递参数”的警告时，这提示你在向对象发送消息时存在类型不匹配问题。如果看到关于不兼容指针类型的消息，请确认你使用的是 `*@"string"*` 而非标准 C 语言的 `*"string"*`。“缺少类型转换”这类警告通常表明你使用了基本数据类型代替对象（或反之）。

若忽略这些警告，应用仍可运行，但一旦在消息中发送这些不兼容的类型，就会导致崩溃。

请注意，这只是一个类定义，因此并未提及字符串数据的存储位置或方式。记住，你其实无需了解对象的内部工作机制。（你最终会爱上这种封装机制的。）但你可能在问：“第 33 页提到的 `–uppercaseString` 方法去哪儿了？”问得好，这引出了 Objective-C 另一个强大且独特的功能：分类。

## 分类之说

像字符串处理类这样重要的类存在一个问题：它们可能会变得非常庞大且笨重。分类通过将类定义拆分为多个部分来避免这个问题。以 `NSString` 为例，主类定义中仅包含最基本的功能。该类的核心内容在这个分类中：

```
@interface NSString (NSStringExtensionMethods)
```

你之前（第 35 页）见过的 `@interface` 后面跟的是类名。只有看到括号时，事情才变得有趣起来。

括号中的名称提示了这里的功能。这个类接口提供了扩展先前定义的 `NSString` 的方法。它在不使字符串基本定义杂乱的前提下，增加了新功能。

**提示：** Objective-C 中的命名是一门艺术。允许使用冗长的名称，比如 `NSStringExtensionMethods` 这个分类名。只要确保名称描述性足够强，让你无需查阅文档就能理解其含义即可。

该字符串分类包含以下方法定义：

```
- (NSString *)uppercaseString;
```

这正是你在第 33 页见到的方法。

但分类还有更酷的玩法。你可以用它们来扩展现有类（即使没有源代码也没问题）。假设你需要让所有字符串变得*超棒*。虽然你还不太清楚“超棒”具体指什么，但你知道自己会经常用到这个功能。

你可以像这样定义自己的分类：

```
@interface NSString (AwesomeMethods)

- (NSString *)awesomeString;

@end
```

你基于现有类 `NSString` 创建分类，并通过在括号内加入 `AwesomeMethods` 为其指定分类名称。

这个新类将拥有普通 `NSString` 的所有功能，同时还能响应一个名为 `–awesomeString` 的额外消息。更妙的是：系统中所有其他字符串对象都能理解这条新消息。如果通过子类实现 `–awesomeString`，你将花费大量时间重构自己的代码来使用新实现，还会遇到无法控制的代码（如 Cocoa API 返回的字符串）带来的问题。分类是一种强大的构造，让你能在不破坏现有代码的前提下扩展系统。

现在你需要的只是让字符串变“超棒”的代码。

## 实现：美丽背后的智慧

每个 `@interface` 都对应一个 `@implementation`。最简单的理解方式是：`@interface` 是类从外部看的样子，而 `@implementation` 是类从内部看的样子。

**注意：** 到目前为止，你一直在看接口头文件的内容。按照惯例，这些文件像 C 语言一样使用 `.h` 扩展名。接口的实现则放在 `.m` 文件中。只需记住：*方法（Methods）*放在以字母 *M* 结尾的文件里。

以下是可能的实现方式：

```
@implementation NSString (AwesomeMethods)

- (NSString *)awesomeString {
    NSString *awesome = [self uppercaseString];
    if (! [self hasSuffix:@"!]) {
        // 添加震撼效果！！！
        awesome = [awesome stringByAppendingString:@"!!!"];
    }
    return awesome;
}

@end
```

现在，你的 Objective-C 代码阅读能力越来越强了。你会看到“超棒”字符串是通过老朋友 `–uppercaseString` 创建的。`–hasSuffix:` 方法检查末尾是否至少有一个感叹号。如果没有，`–stringByAppendingString:` 方法就会添加必要的震撼效果。但这里的 `self` 是什么意思呢？

每个方法实现都会接收一个名为 `self` 的隐藏参数，它引用接收消息的对象。由于你的方法是 `NSString` 类的一部分，`self` 指向该类的实例。（是的，你在方法里和自己对话，这完全没问题。）

**注意：** C++、Java 和 PHP 都使用 `this` 来实现这种自引用。

一旦实现了这个分类方法，你的代码就可以这样调用它：

```
NSString *ordinaryString = @"typing power";
NSString *excitingString = [ordinaryString awesomeString];
```

`excitingString` 对象的值将是“TYPING POWER!!!”。

分类还能帮助你避免代码中的重复。假设你经常检查字符串是否为“超棒”状态。你就会反复编写这样的代码：

```
NSString *myString = @"something";
if ([myString isEqualToString:[myString awesomeString]]) {
    // 抱歉，myString 不是超棒字符串
}

NSString *myOtherString = @"SOMETHING ELSE!!!";
if ([myOtherString isEqualToString:[myOtherString awesomeString]]) {
    // 而 myOtherString 则是真正的超棒字符串
}
```

这样写既麻烦又难读。因此，在 `NSString` 的分类接口中添加以下内容：

```
@interface NSString (AwesomeMethods)

- (NSString *)awesomeString;
- (BOOL)isAwesomeString;

@end
```

并在实现中添加以下内容：

```
- (BOOL)isAwesomeString {
    return [self isEqualToString:[self awesomeString]];
}
```

这大大简化了你的检查代码：

```
NSString *myString = @"something";
if ([myString isAwesomeString]) {
    // 抱歉，myString 不是超棒字符串
}

NSString *myOtherString = @"SOMETHING ELSE!!!";
if ([myOtherString isAwesomeString]) {
    // 而 myOtherString 则是真正的超棒字符串
}
```

同样重要的是要注意，至此你并未真正创建新类。你只是在系统框架提供的代码基础上进行扩展，以满足自身需求。

Cocoa Touch 本身就很棒；你只是让它变得更棒。

## 创建新类

如你所见，分类能完成很多工作，并提供了一种扩展现有代码（非你编写）的绝佳方式。但它们有一个主要限制：你不能在分类定义中添加实例变量。对于 `NSString` 类来说，你又怎么能添加呢？类接口完全没有说明字符串是如何存储的。

是时候学习如何创建新类并扩展 Cocoa 层次结构了。为了展示具体做法，你将创建一个能让你控制感叹号数量的新类，就像“THIS IS GOING TO BE AWESOME!!!!!!!”这个短语那样。

首先要做的是为这个新类创建 `@interface`：

```
@interface AwesomeStringMaker : NSObject
{
    NSNumber *exclamationCount;
    NSString *originalString;
}

- (NSNumber *)exclamationCount;
- (void)setExclamationCount:(NSNumber *)newExclamationCount;
- (NSString *)originalString;
- (void)setOriginalString:(NSString *)newOriginalString;

@end
```



```objc
- (NSString *)awesomeString;

@end
```

这段代码看起来与第 39 页的类分类相似。这次，括号中的分类名称消失了，而花括号 `{ }` 中增加了一些新代码。类的数据就是在这些花括号之间指定的。

这个示例有两个实例变量：一个用于跟踪感叹号数量的数值，以及一个你想要变得「酷炫」的字符串。

## 创建新类

**注意：** 你会发现类数据有多种称呼方式。Objective-C 开发者会混用 `instancevariable`、`ivar` 和 `property` 这几个术语。如果你和长期使用 C++ 的开发者交流，可能会听到 `membervariables`。Java 的忠实用户则称之为 `fields`。

使用哪个术语并不重要；它本质上仍然只是与类每个实例相关联的一块内存。

接口末尾的幾個新方法允许你读取和写入这个新类管理的数据。这些方法被称为*存取器*，因为它们让你能够访问类的内部信息。按照惯例，读取实例变量的方法直接使用变量名，而更新实例变量的方法则以 `set` 为前缀。在其他语言中，这些方法被称为 *getters* 和 *setters*。

只有该类的实现才能轻松访问所有实例数据。如果没有存取器方法，这些数据实际上是隐藏的。在大多数情况下，这是一件好事，尤其是当你拥有不想暴露给调用者的私有数据时。

现在来看 `AwesomeStringMaker` 类的 `@implementation`：

```objc
@implementation AwesomeStringMaker

- (NSNumber *)exclamationCount {
    return exclamationCount;
}

- (void)setExclamationCount:(NSNumber *)newExclamationCount {
    if (exclamationCount != newExclamationCount) {
        [exclamationCount release];
        exclamationCount = [newExclamationCount retain];
    }
}

- (NSString *)originalString {
    return originalString;
}

- (void)setOriginalString:(NSString *)newOriginalString {
    if (originalString != newOriginalString) {
        [originalString release];
        originalString = [newOriginalString copy];
    }
}

- (NSString *)awesomeString {
    NSString *awesome = [originalString uppercaseString];
    NSUInteger length = [awesome length];
    NSInteger padding = [exclamationCount unsignedIntValue];
    NSString *moreAwesome = [awesome stringByPaddingToLength:(length + padding) withString:@"!" startingAtIndex:0];
    return moreAwesome;
}

@end
```

借助 `AwesomeStringMaker` 的强大功能，你可以这样使用该类：

```objc
AwesomeStringMaker *myAwesomeStringMaker = [[AwesomeStringMaker alloc] init];
[myAwesomeStringMaker setExclamationCount:[NSNumber numberWithFloat:8.0f]];
[myAwesomeStringMaker setOriginalString:@"typing power"];
NSString *myAwesomeString = [myAwesomeStringMaker awesomeString];
// myAwesomeString 现在包含 "TYPING POWER!!!!!!!!"
[myAwesomeStringMaker release];
```

**提示：** 在学习 Objective-C 时，请注意哪个对象接收哪个消息。如果你尝试向 `NSString` 的实例而不是 `AwesomeStringMaker` 发送 `-setExclamationCount:` 消息，编译代码时会收到“方法未找到”的警告。在运行时，你的应用会因为 `NSString` 类不知道如何处理你发送的消息而在 `objc_msgSend` 中抛出异常并崩溃。

## 管理内存

如果你看一下类中 `exclamationCount` 和 `originalString` 的存取器，会发现实例变量上调用了某些新方法。`retain`、`copy` 和 `release` 消息极其重要：你需要用它们来管理对象的内存使用。

**注意：** 内存也是 Objective-C 中较难理解的概念之一，所以如果一开始没弄明白，不必灰心。

正如你之前了解的，在你使用 iPhone 的每一秒，都有成千上万个对象被创建和销毁。而每个对象都会占用一块内存，这在移动设备上是宝贵的资源。你的台式电脑可能以 GB 计量内存容量，但手机只有几百 MB。

如果你不注意内存使用，iPhone 操作系统就会关闭你的应用。如果不这样做，系统最终会陷入停滞，你只能重启手机。这对于正在等待重要电话的你来说，会相当不便。

那么，这些 `-retain`、`-release` 和 `-copy` 方法是如何管理内存的呢？

内存中的每个对象都维护着一个计数器。这个计数器跟踪有多少其他对象正在使用该对象。当你想要使用一个对象时，必须告知该对象，以便它更新计数器，而你要做的就是发送 `-retain` 消息。

发送该消息后，对象会更新其保留计数，并且该对象不会被删除。如果你想知道有多少其他对象持有对该对象的引用，可以向它发送 `-retainCount` 消息，它会返回计数器的当前值。对象初始分配时，其保留计数被设置为 `1`。

现在你知道了如何让对象保留在内存中，接下来需要一种方法，在你用完它们后将其清除。这就是 `-release` 消息的作用。它的作用与 `-retain` 正好相反；它不是增加，而是*减少*保留计数。你是在告诉对象你不再需要它了。

当你的应用在 Cocoa Touch 环境中运行时，系统会定期检查对象的保留计数。计数为零的对象不再被任何其他对象使用，因此系统可以删除它们并回收其内存。

既然你已经熟悉了 retain 和 release 的机制，让我们更详细地看一下存取器：

```objc
if (exclamationCount != newExclamationCount) {
```

这行代码是一个简单的性能优化。如果旧对象和新对象是同一个，就无需调整保留计数。

**提示：** 这段代码比较的是指针地址，这是一种快速（且有效）检查两个对象是否相同的方法。事实上，`NSObject` 的 `-isEqual` 方法也使用了同样的方法。（对于许多类，比如 `NSString`，检查相等性比检查指针要复杂得多。）

首先要做的是通过发送 `-release` 消息，告诉旧的实例变量你不再需要它：

```objc
[exclamationCount release];
```

现在你已经释放了实例变量，就无法保证该变量仍然有效。如果你尝试向 `exclamationCount` 发送更多消息，应用很可能会崩溃。因此，下一步是保留传入存取器的新对象。`retain` 方法会返回自身，因此该返回值用于更新 `exclamationCount` 的实例变量。此时，再次向该对象发送消息就是安全的了：

```objc
exclamationCount = [newExclamationCount retain];
```

`-retain` 消息的一种变体是 `-copy` 消息。和 retain 一样，你会拥有对该对象的独有引用，该引用在你决定释放之前不会消失。区别在于，你持有的是原始对象数据的一个副本。

这里有一个 `-copy` 派上用场的场景。对于 `originalString` 实例变量，你不希望使用 `-retain`，因为原始字符串可能被另一个对象修改。请记住，如果你与许多其他对象共享一个引用，并且其中一个对象修改了该字符串，那么当你调用 `-awesomeString` 时就会看到这个变化。



你想要拥有字符串的独立副本，因此应该使用以下代码：`originalString = [newOriginalString copy];`

如果另一个对象修改了 `newOriginalString` 所指向的对象，你不会受到影响。

**服用 `nil` 这颗药丸**

有时你想彻底清空一个实例变量。可能是因为不再使用该对象，想要释放内存；或者你正在应用程序中建模某种状态，而对象的缺失本身具有意义。你可以使用一个名为 `nil` 的特殊对象来清空变量。如果一个引用对象的变量包含 `nil` 值，则表示没有对象。

`nil` 对象具有一种有趣的行为，会影响你在管理内存时对它们的使用。当你向 `nil` 对象发送消息时，该消息会被忽略，并返回 `nil` 结果。这种技术通常被称为 `nil` 目标消息，如下所示：

```
NSString *missingString = nil;

NSString *excitingString = [missingString awesomeString];

// excitingString 的值也是 nil
```

当你在编写使用 `retain`、`copy` 和 `release` 的访问器时，这一特性可以帮助清理内存。要了解其工作原理，请将 `newExclamationCount` 替换为 `nil`，代码将变成：

```
if (exclamationCount != nil) {
    [exclamationCount release];
    exclamationCount = [nil retain];
}
```

如果当前计数值不是 `nil`，则会释放 `exclamationCount` 对象。然后，`retain` 消息返回的 `nil` 结果被用来设置新的计数值。

`nil` 目标消息还能帮助防止在初始化失败后的代码中发生崩溃。（初始化失败的对象会被赋值为 `nil`，以表示它们不存在。）当你想要管理某种状态时，也可以利用 `nil` 对象。例如，在一个视图中使用 `middleName` 实例变量：如果该变量的值是 `nil`，你就知道不需要显示一个人的中间名。

某些代码还会利用 `nil` 的数值：零。以 `exclamationCount` 为例，当它为 `nil` 时，`unsignedIntValue` 消息会被忽略，并返回 `nil`：`NSInteger padding = [nil unsignedIntValue];`

// padding 的值是零

当内边距为 0 时，不会使用任何感叹号，这对于实例变量没有对象的情况来说似乎是一种合理的行为。另一方面，如果你的代码需要一个对象，但尚未设置，你应该使用类似下面的代码：

```
if (myObject) {
    [myObject doSomething];
}
else {
    NSLog(@"无法执行 doSomething，因为 myObject 是 nil！");
}
```

再次说明，这段代码依赖于 `nil` 对象值为零的事实。如果 `myObject` 有值，其非零值将允许发送 `doSomething` 消息。

**提示：** `nil` 对象也可能是令人费解的根源。如果你忘记初始化一个变量，在向它发送消息时，你会疑惑为什么对象实例没有按预期工作。当你进入调试器、显示该对象并看到“无法访问地址 0x0 的内存”时，顿悟的时刻就到了。你的对象值为零。

**轻松使用 Autorelease**

Autorelease 与松开你的汽车无关。在处理临时对象时，所有那些保留、复制和释放操作都是相当繁琐的编程工作。通常，你只需要对象存在足够长的时间来完成某些工作。例如，许多对象从不会超出方法实现的作用域。

那些聪明又懒惰的、提出 Objective-C 的程序员们想出了一个变通方法，叫做 `autorelease` 方法。要了解其工作原理，首先看看 `myAwesomeStringMaker` 对象是如何管理的：

```
AwesomeStringMaker *myAwesomeStringMaker = [[AwesomeStringMaker alloc] init];

// 对 myAwesomeStringMaker 进行一些操作

[myAwesomeStringMaker release];
```

这段代码可以通过使用 `autorelease` 消息来简化：

```
AwesomeStringMaker *myAwesomeStringMaker = [[[AwesomeStringMaker alloc] init] autorelease];

// 对 myAwesomeStringMaker 进行一些操作
```

在对象被分配和初始化之后，它会收到 `autorelease` 消息。一个被自动释放的对象保证在当前方法执行期间保留在内存中。之后，它将在无需你直接干预的情况下被删除。

这一特性大大简化了你的工作。在这个例子中，`myAwesomeStringMaker` 只在短时间内需要，因此自动释放会自动清理对象。如果你在分配对象和方法结束之间对 `myAwesomeStringMaker` 进行了大量操作，很容易忘记发送 `release` 消息。

由于其他程序员和你一样聪明又懒惰，许多方法返回的都是自动释放的对象。前提是你可能不会长时间需要该对象，因此最好让系统来进行清理。

按照惯例，只有名称中包含 `init` 或 `copy` 的方法才会返回非自动释放的对象。如果你使用了这些方法之一，就必须关注内存的情况。更多细节请参见下面的方框。

## 智者箴言：牢记规则

长久以来使用 Cocoa 的程序员都会引用以下规则（最初由 Don Yacktman 编写），每当有人询问关于如何管理内存的问题时：

- 如果你分配、复制或保留了某个对象，那么你就有责任在不再需要该对象时，使用 `release` 或 `autorelease` 来释放它。如果你没有分配、复制或保留某个对象，那么就不应该释放它。
- 当你收到一个对象（作为方法调用的结果）时，它通常会在你的方法结束前保持有效，并且可以安全地作为方法的结果返回。如果你需要该对象的生存期更长——例如，你计划将其存储在一个实例变量中——那么你必须 `retain` 或 `copy` 该对象。
- 当你想要返回一个对象但又希望放弃对其的所有权时，使用 `autorelease` 而非 `release`。出于性能考虑，在可以的情况下使用 `release`。
- 当你想要防止一个对象因正在执行的操作的副作用而被销毁时，使用 `retain` 和 `release`（或 `autorelease`）。

**管理内存**

**属性与点语法**

随着你的 Objective-C 编码水平不断提高，你会发现类中的访问器开始变得非常多。它们是另一种无聊的代码，懒惰的程序员想要避免，尤其是那些一遍又一遍重复的 `retain` 和 `release` 内容。你可以利用另一种称为*声明属性*的捷径。

这一语言特性提供了一种简单的方式来指定、实现和使用类的访问器方法。

当你使用属性时，类的接口和实现都会发生变化：

```
@interface AwesomeString : NSString
{
    NSNumber *exclamationCount;
    NSString *originalString;
}

@property (nonatomic, retain) NSNumber *exclamationCount;
@property (nonatomic, copy) NSString *originalString;

- (NSString *)awesomeString;

@end
```

每个属性都使用括号内的属性进行配置。`nonatomic` 属性使访问器速度更快；只有在处理多线程时才需要原子属性。`retain` 属性会导致实例变量在赋值期间使用 retain/release 模式。同样，`copy` 属性使用 copy/release 模式。你刚刚删除了两行代码，但等等，还有更多好处！



