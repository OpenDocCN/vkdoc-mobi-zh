# 第 2 章：Objective-C 简介

`NSString *name = [NSString stringWithFormat:@"%@, %@", lastName, firstName]; return name;`

```
- (NSString *)firstName
{
    return firstName;
}

- (NSString *)lastName
{
    return lastName;
}

- (NSInteger)birthYear
{
    return birthYear;
}
```

现在，我们可以通过向 `Person` 对象发送 `firstName` 消息来获取其名字。为了能够设置这些值，我们需要定义其他方法。将以下方法声明添加到 `Person.h` 中：

```
@interface Person : NSObject {
    NSString *firstName;
    NSString *lastName;
    NSInteger birthYear;
}

- (id)initWithFirstName:(NSString *)firstName
               lastName:(NSString *)lastName
              birthYear:(NSInteger)birthYear;
- (NSString *)firstName;
- (void)setFirstName:(NSString *)firstName;
- (NSString *)lastName;
- (void)setLastName:(NSString *)lastName;
- (NSInteger)birthYear;
- (void)setBirthYear:(NSInteger)birthYear;
- (NSString *)displayName;

@end
```

按照惯例，需要在变量名称前加上 `set`，并将首字母大写。实现这些方法也很直接，但我们必须重命名参数，以避免它与实例变量重名。在 Objective-C 中，头文件和实现文件之间需要频繁切换。如果你的 Mac 显示器足够宽，Xcode 中的辅助编辑器（Assistant Editor）会有所帮助。打开 `Person.m` 并选择 View ▸ Assistant Editor ▸ Show Assistant Editor，或者按下 ⌘+Control+Return。屏幕右侧会打开一个辅助编辑窗格。如果辅助编辑器没有打开 `Person.h`，请按下 ⌘+Shift+Option+Z 切换到“对应文件（Counterparts）”模式，该模式会自动打开当前实现文件对应的头文件。

既然你已经打开了两个文件，那么在头文件中添加方法声明，然后在实现文件中实现它就会变得容易得多。在 `Person.h` 打开的情况下，为我们的三个设置器方法添加实现：

```
- (void)setFirstName:(NSString *)name
{
    firstName = name;
}

- (void)setLastName:(NSString *)name
{
    lastName = name;
}

- (void)setBirthYear:(NSInteger)year
{
    birthYear = year;
}

@end
```

尽管创建这些方法本身并不复杂，但过程却相当繁琐。需要大量输入和文件切换，如果某天你想更改某个名称，则需要修改很多地方。在 Objective-C 的早期阶段，这已经是最佳方案了。有些开发者会使用第三方应用程序来创建这些用于获取和设置实例变量的方法，这足以说明其繁琐程度。幸运的是，Apple 为这门语言添加了一些新特性，使其变得更加便捷。

## 属性

属性是在类中定义存取器方法的一种方式。你可以使用属性来定义实例变量的获取器和设置器，而无需分别声明。以下是属性的一个示例：

`@property (nonatomic, copy) NSString *firstName;`

这个声明告诉我们几件事。首先，它指明了变量的类型和名称。头文件中的这一行等同于以下两行代码：

```
- (NSString *)firstName;
- (void)setFirstName:(NSString *)firstName;
```

声明了属性后，我们无需显式声明这些方法即可引用它们（并实现它们）。其次，圆括号中的词语定义了一些关于变量如何使用的信息。我们使用 `nonatomic` 来定义线程的访问规则。现在不必深入了解，但通常来说，`atomic` 对于多线程应用更安全，但速度比 `nonatomic` 慢。大多数情况下，你会使用 `nonatomic`。下一个词是 `copy`，表示在设置字符串时我们会复制一份。我们通常对字符串使用 `copy`，以确保字符串在设置后不会被修改。除了 `copy`，还有一些用于内存管理的关键字可供选择，稍后我们会讨论。

你还可以传入第三个词语，可以是 `readonly` 或 `readwrite`。如果使用 `readonly`，则不会创建设置器（`readwrite` 是默认值）。为方便参考，表 2-1 列出了你可以设置的属性（**加粗**的选项为默认值）。

**表 2-1：** *Objective-C 属性属性*

| 内存管理语义 | 读/写语义 | 原子性 |
| --- | --- | --- |
| `assign` | **`readwrite`** | **`atomic`** |
| `weak`（iOS 5+，已启用 ARC 且 Xcode 4.2 或更新版本） | `readonly` | `nonatomic` |
| `__unsafe_unretained`（iOS 4.3 及更老版本且 Xcode 4.2 或更新版本） | | |
| `strong`（iOS 5+ 且 Xcode 4.2 或更新版本） | | |
| `retain`（iOS 4.3 及更老版本） | | |
| `copy` | | |

如你所见，`weak`、`__unsafe_unretained` 和 `strong` 都需要 Xcode 4.2。要使用 `weak`，你必须使用 ARC（一种自动化内存管理技术）。稍后我们会更详细地讨论 ARC。

如果你希望修改获取器和设置器方法的名称，也可以自行定义。如果不定义，它们会使用默认名称。但例如，如果你的变量是布尔类型（`BOOL`），你可能希望获取器使用 `is` 这个词。以下是头文件中这样的属性声明行：

`@property (getter = isFoo) BOOL foo;`

这一行等同于以下几行代码：

```
- (BOOL)isFoo;
- (void)setFoo:(BOOL)foo;
```

类似地，如果你想更改设置器方法的名称，可以使用 `setter =` 后跟你想要的名称（尽管实践中很少这样做）。通过使用这些属性，你可以用属性来声明你的对象所需的大多数（如果不是全部）存取器方法。

### 为你生成代码

属性固然好用，但它们只帮你处理了方法的声明。仍然需要通过样板代码来实现这些方法，这些代码仅仅按需设置变量。幸运的是，Apple 在将属性添加到语言中的同时，也提供了一种实现这些方法的方式：`@synthesize` 编译器指令。在实现代码块中，你可以使用 `@synthesize` 告诉编译器为你生成这些方法。它会根据你设置的属性属性来适当地生成方法。如果你没有为属性准备实例变量，使用 `@synthesize` 会自动为你创建一个。下面的类是一个包含一个属性的对象，完全无需显式实现任何一个方法即可完成实现：

```
@interface BoolWrapper: NSObject {
    BOOL _value;
}
@property BOOL value;
@end

@implementation BoolWrapper
@synthesize value = _value;
@end
```

这定义了一个 `BOOL` 类型的属性 `value`，并使用实例变量 `_value` 作为存储。如果我们省略声明 `_value` 作为实例变量，`@synthesize` 这行代码会为我们创建它。我们可以向 `BoolWrapper` 的实例发送 `value` 和 `setValue:` 消息，而无需编写这些方法。

这就是属性的真正闪光之处。你不仅无需实现样板代码，而且当未来需要重构代码时，它也让事情变得容易得多。它还能大大降低代码出错的概率，因为编译器生成的代码是经过充分测试的——而且编译器既不会打错字，也不会疲劳。

### 内存管理

内存是 iOS 设备上的宝贵资源。初代 iPhone 拥有 128MB 的 RAM，其中大部分被操作系统占用。四年后，iPhone 4S 拥有 512MB 的 RAM，仍然远低于运行 Mac OS X 的现代系统。正因如此，我们如何使用可用内存至关重要。虽然本节内容可能不那么令人兴奋，但理解 iOS 上内存管理的工作原理对于排查性能问题至关重要。


设备上的内存分为两种类型：栈内存和堆内存。栈随着代码的执行而被填满；当一个函数调用另一个函数时，它会在栈上压入一个栈帧，其中包含该函数的局部变量和所需的存储空间。当函数执行完毕，该栈帧会从栈中弹出，这些空间会被回收以供将来使用。栈空间非常有限，一旦耗尽，程序就会因栈溢出而崩溃（这也就是著名编程问答网站名字的由来）。虽然栈内存使用方便，但它有一个巨大的缺点：当栈帧弹出时，其上的所有内容都会消失。这对于对象来说并不适用。通常，当我们在一个方法中创建对象时，我们期望该方法执行完毕后该对象仍然可用。然而，如果对象是在栈上创建的，它将在方法结束时被销毁。

因此，所有的 Objective-C 对象都是在堆上创建的。虽然可以手动覆盖对象创建过程并在栈上创建对象，但在实践中这并无必要。堆内存与栈内存不同，我们必须向操作系统申请堆内存。在 C 语言中，我们使用`malloc()`函数来分配一块内存。你可能会注意到，这与你在创建新对象时向类发送的`alloc`消息有相似之处。

与栈不同，堆在使用完毕后不会自行清理；所有通过`malloc()`分配的内存都必须使用`free()`来释放。一旦你释放了手动分配的内存块，该空间就可以再次使用。对象也是在堆上创建的，但这比分配任意内存更复杂。我们知道希望对象持久存在，但如何判断它们应该存在多久，以便释放其内存呢？

## 垃圾回收

一种实现方式称为垃圾回收。用于 Android 及其他受控系统的 Java 就采用了垃圾回收机制，其原理是跟踪你的对象，并在检测到它们不再被使用时定期销毁。虽然这听起来简单，但存在一个权衡：当垃圾回收器运行时，你的应用会被暂停，从而导致性能问题。有一些方法可以缓解这一问题，但没有一种方法是完美的。另一个问题是，如果两个对象相互引用，垃圾回收器就无法判断它们何时不再被使用，因为其中任何一个都可能正在使用另一个。这被称为循环保留，其最终结果是大量内存被不可用的对象占用。Apple 曾在 Mac OS X 上将垃圾回收引入 Objective-C，但效果参差不齐，如今已不再推荐使用。

## 引用计数

Objective-C 采用另一种方法来跟踪对象：引用计数。引用计数通过计算对某个对象的引用数量来工作，当该数量变为 0 时，对象被销毁。在 Objective-C 中，这被称为对象的保留计数。所有对象在生命周期开始时保留计数为 1，当它变为 0 时，对象被销毁，其内存返还给系统。以下是一个典型的保留计数场景示例：

```
- (void)doSomething
{
    MyObject *foo = [[MyObject alloc] init];
    [foo doAnImportantTask];
    [foo release];
}
```

在这个例子中，`foo`创建时保留计数为 1；然后，在我们结束之前，我们向其发送了`release`消息，这会将保留计数减 1。然而，需要特别注意的一点是，我们实际上无法确知`foo`此时会被销毁。在发送`release`消息之前，我们向`foo`发送了`doAnImportantTask`消息。在该方法中，`foo`可能已经被保留（通过向其发送`retain`消息），这会使它的保留计数增加。通用规则是，在任何给定的方法中，我们必须平衡`retain`和`release`的调用。

由于分配`foo`使其保留计数为 1，我们在方法结束前发送了`release`消息使其变为 0。我们仅当保留或创建了某个对象时才需要释放它。在 Cocoa Touch 中，存在一种命名约定，用于指示何时需要释放对象：如果方法名以`alloc`、`new`、`copy`或`create`开头，则预期返回的对象保留计数为 +1，你需要在用完后释放它。所有其他方法应返回不需要释放的对象。

## 自动释放池

尽管引用计数系统很有效，但有时你希望在返回对象的同时释放它。你经常会看到作为对象工厂的类方法：

```
+ (MyObject *)object;
```

`object`方法返回一个`MyObject`实例，但由于其名称不以表示所有权的前缀开头，我们不应返回一个保留计数为 +1 的对象。为了解决这个问题，`object`的典型实现如下：

```
+ (MyObject *)object
{
    MyObject *object = [[MyObject alloc] init];
    return [object autorelease];
}
```

乍一看，这似乎意味着`object`会立即被释放：它以保留计数 1 创建，然后发送了`autorelease`消息，这使得它的保留计数看起来会减少，从而变为 0 并被释放。自动释放一个对象确实会减少其保留计数……只是不是立即生效。对象实际被释放的时间点并不太重要，因为这取决于系统的繁忙程度以及你的代码编写方式，但关键是它不会在你的栈帧消失之前发生，因此自动释放的对象在方法的持续时间内是安全可用的。

## 自动引用计数

回顾一下我们为“Hello, World!”示例应用编写的代码，你可能会注意到一点：你从未在对象上调用过`retain`或`release`。事实上，其中完全没有内存管理代码！你可以认为自己非常幸运：你正在一个所有代码都为你写好的时代学习内存管理。

正如属性与`@synthesize`为你编写 getter 和 setter 一样，自动引用计数为你编写内存管理代码。

它是如何工作的呢？考虑平衡`retain`和`release`调用的总体目标。一般来说，一个对象的保留计数在方法开始和结束时应当是相同的。自动引用计数所做的是：当创建一个指向对象的指针时，保留该对象；当该指针超出作用域时，释放该对象。以下代码：

```
{
    MyObject *object = [MyObject object];
}
```

通过 ARC 将生成如下代码：

```
{
    MyObject *object = [[MyObjet object] retain];
    [object release];
}
```

ARC 知道哪些方法返回已保留的对象，并据此进行处理。它甚至可以利用对内存管理代码的了解来优化应用的内存使用并加快执行速度。它生成的`retain`和`release`调用也比你自己发送的消息更快。总而言之，ARC 与属性一样，将工作从你（程序员）转移到了编译器。请相信我，你花在编写内存管理代码上的时间越少越好。在引入 ARC 之前的 iOS 应用中，大部分崩溃都是由错误的内存管理代码引起的。考虑以下代码片段：

```
- (void)importantTask
{
    MyObject *object = [[MyObject alloc] init];
    [object doSomething];
    [object release];
```


`[object doSomethingElse];`

`}`

假设`doSomething`没有错误地保留`object`，我们就遇到了一个问题。对`release`的调用会导致`object`的引用计数归零，从而销毁它，但在此之后，我们又对`object`调用了`doSomethingElse`。由于`object`只是一个指向对象的指针，它仍然保存着该对象的内存地址。如果试图访问那块内存，系统会知道它已经从你那里收回了。因为你不能访问系统未分配给你的内存，所以这次对`doSomethingElse`的调用会导致应用程序崩溃。

自动引用计数（ARC）几乎消除了这类错误，为开发者省去了无数小时的痛苦。

## ARC 与属性

关于 ARC 的内存管理，你需要了解的一点是如何将其与属性（properties）配合使用。你可以为属性设置的一类属性（attributes）是其内存管理语义（memory management semantics）。使用`strong`属性会导致属性在被设置时被保留（retained），并且在对象被释放（deallocated）之前不会被释放。这是属性最常见的用法。

然而，一个常见的问题是，如果两个对象彼此之间都有强引用（strong reference），那么两者都不会被释放，这就像垃圾回收一样。解决方法是让其中一个对象引用另一个对象但不保留它。在 iOS 5 之前，`assign`属性被用于此目的。但问题在于，如果使用`assign`，你可能会在对象被释放后仍然持有它的引用，当尝试使用它时，应用程序会崩溃。在 iOS 5 中，苹果引入了`weak`属性，它是对一个对象但不保留它的引用。然而，当被引用的对象被释放时，弱引用（weak reference）也会消失，这样你就不会尝试在已释放的对象上调用方法。弱引用允许你临时存储指向对象的指针，而无需担心影响它们的生命周期或被其影响。

当一个弱引用的对象被销毁时，所有指向它的弱指针都会被设置为`nil`，即 Objective-C 中与空对象（null object）等价的类型。向`nil`发送消息是安全的，因此使用弱引用时，你不需要检查引用是否指向一个对象。向`nil`发送消息，如果消息有返回值，所有对象类型的返回值都会是`nil`，所有基本类型的返回值都会是`0`。

[www.it-ebooks.info](http://www.it-ebooks.info/)

