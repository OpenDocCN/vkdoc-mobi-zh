# Objective-C 中的类方法与对象构造

在 `+(NSNumber*)number` 方法体中，`self` 变量指向类对象（`RandomSignature`）。这正是 Java 类方法和 Objective-C 类方法的不同之处。在 Java 中，类方法没有对象上下文（没有 `this` 变量）。而在 Objective-C 中，类消息发送给类对象的方式与发送给普通对象相同。`+(NSNumber*)number` 方法体中的 `[self string]` 语句会调用类方法 `+(NSString*)string`，而不是实例方法 `-(NSString*)string`。与 Java 静态方法真正等价的是 C 函数。

这一区别对于继承至关重要。类对象继承类方法的方式与实例方法继承相同。如果类 `B` 是类 `A` 的子类，且类 `A` 定义了一个类方法，则类 `B` 会继承该类方法。类 `B` 也可以重写该类方法。代码清单 3-16 展示了这种关系。

**代码清单 3-16. 类方法继承**

```objectivec
@interface Classy : NSObject

+ (void)greeting;
+ (NSString*)salutation;

@end

@implementation Classy

+ (void)greeting
{
    NSLog(@"%@, world!",[self salutation]);
}

+ (NSString*)salutation
{
    return (@"Greetings");
}

@end

@interface Classic : Classy

+ (NSString*)salutation;

@end

@implementation Classic

+ (NSString*)salutation
{
    return (@"Hello");
}

@end

...

[Classy greeting];   // 输出 "Greetings, world!"
[Classic greeting];  // 输出 "Hello, world!"
```

语句 `[Classy greeting]` 将 `greeting` 消息发送给 `Classy` 类对象。该方法向自身发送 `salutation` 消息，并用返回值构建日志消息 "Greetings, world!"。

语句 `[Classic greeting]` 将 `greeting` 消息发送给 `Classic` 类对象。`Classic` 并未定义类方法 `+(void)greeting`，但它从 `Classy` 继承了一个。当在此上下文中执行 `+(void)greeting` 时，`self` 变量指向的是 `Classic`，而非 `Classy`。向 `self` 发送 `salutation` 消息会调用 `Classic` 重写的 `-(NSString*)salutation` 方法，因此记录的日志消息是 "Hello, world!"。

> **注意：** 如果代码清单 3-16 中的语句是 `NSLog(@"%@, world!",[Classy salutation])` 而非 `NSLog(@"%@, world!",[self salutation])`，那么消息将始终是 "Greetings, world!"。这是因为 `Classy` 是一个常量，始终指向唯一的 `Classy` 类对象，而 `self` 则指向接收 `greeting` 消息的类对象。

尽管存在细微的技术差异，但 Objective-C 中的类方法被用于许多与 Java 静态方法相同的用途。返回单例对象的方法、工厂方法、对象池以及便捷工具都是类方法的常见用途。

### 构造对象

你可能以为我会在本章更早的地方解释实例化对象的语法，而不是让你费力阅读实例变量、关于 `self` 变量的解释以及类方法。我没有这样做的原因很简单：实例化对象并没有专门的语法。

秉承其极简主义哲学，Objective-C 让类设计者自行决定如何创建和初始化对象。在 Cocoa 框架中，这是通过工厂类方法和一些编写初始化器（构造器）方法的惯例组合来实现的。

> **注意：** 你会反复看到这种极简模式。Java 定义了特定的语法并强制执行预定义的规则，用于对象的创建、序列化、复制等操作。相比之下，Objective-C 只提供最基础的内容，将实现决策留给类设计者。你不能在 Java 中改变对象创建的方式，但在 Objective-C 中可以。你可能不需要经常这样做，但确实能够产生巨大效果。

创建对象是一个两步过程。首先，为对象结构分配内存。然后实例被初始化。代码清单 3-17 展示了 `RandomSequence` 类的重写版本，包含两个初始化器（构造器）。

**代码清单 3-17. 对象初始化**

**Java**

```java
public class RandomSequence
{
    long seed;

    public RandomSequence()
    {
        seed = 1;
    }

    public RandomSequence( long startingSeed )
    {
        seed = startingSeed;
    }
}

...

RandomSequence r1 = new RandomSequence();
RandomSequence r2 = new RandomSequence(-43);
```

**Objective-C**

```objectivec
@interface RandomSequence : NSObject {
    long long seed;
}

- (id)init;
- (id)initWithSeed:(long long)startingSeed;

@end

@implementation RandomSequence

- (id)init
{
    self = [super init];
    if (self != nil) {
        seed = 1; // 默认种子
    }
    return (self);
}

- (id)initWithSeed:(long long)startingSeed
{
    self = [super init];
    if (self != nil) {
        seed = startingSeed;
    }
    return (self);
}

@end

...

RandomSequence *r1 = [[RandomSequence alloc] init];
RandomSequence *r2 = [[RandomSequence alloc] initWithSeed:-43];
RandomSequence *r3 = [RandomSequence new];
```

`[RandomSequence alloc]` 语句通过向 `RandomSequence` 类对象发送 `alloc` 消息来启动过程。根类 `NSObject` 实现了 `+(id)alloc` 类方法，该方法被所有子类继承。`+(id)alloc` 使用类引用为新对象分配所需内存，设置其 `isa` 变量，将所有其余实例变量填充为零，并返回指向新分配对象的指针。此时，对象已存在，并属于所请求的类型。然而，该对象尚未被初始化。

接下来，`init` 消息被发送给新创建的对象。该消息负责初始化对象。一旦 `init` 返回，对象即可使用。

代码清单 3-17 中创建对象 `r1`、`r2` 和 `r3` 的三个语句演示了创建新对象的常见方式。变量 `r1` 被赋值为一个新对象，该对象在无参数的情况下分配和初始化，等同于 Java 语句 `r1 = new RandomSequence()`。

`r2` 变体调用了另一个初始化器，传入额外的参数用于初始化对象。与 Java 类似，你可以根据需要创建任意多的额外初始化器（构造器）。

创建对象 `r3` 所使用的简写形式在功能上与创建 `r1` 的形式完全相同。根类 `NSObject` 实现了一个 `+(id)new` 类方法，该方法首先调用 `+(id)alloc`，然后在新创建的对象返回前向其发送 `-(id)init` 方法。它仅适用于创建可以使用其 `-(id)init` 方法（即无参数）构造的对象，但确实能节省一些代码。

> **警告：** 对象构造的自定义始终通过重写或定义类的 `-(id)init...` 方法来完成。切勿重写或尝试拦截 `+(id)alloc` 或 `+(id)new` 类方法。

### 编写 init 方法

要编写 Objective-C 初始化器（构造器），你的方法必须满足一个四部分约定：

1.  初始化器必须调用其超类的初始化器。
2.  它必须更新其 `self` 变量。
3.  它必须检查 `nil` 对象。
4.  它必须返回指向自身的指针。

约定的前两部分通过语句 `self = [super init]` 实现。显然，每个初始化器在继续执行之前都必须调用其超类的初始化器。在 Java 中，这一约定由语言本身保证。在 Objective-C 中，你需要负责调用它。

更新 `self` 对 Java 程序员来说可能有些怪异，但这是实现类簇的关键。类簇将在第 22 章详细解释。你可能想省略对 `self` 的赋值。不要这样做。保留赋值实际上会使代码更精简、速度更快，而且你不会违反类簇约定。



■ 注意  
`init` 方法的返回值传统上是 `id`。从逻辑上讲，`init` 应始终返回指向该类或其子类的指针。然而，将返回值声明为类指针类型（例如 `-(BaseClass*)init`）会给子类带来困难。子类必须调用 `[super init]` 并将其赋值给 `self`。如果 `[super init]` 返回 `BaseClass*` 类型的值，子类将无法在不进行类型转换的情况下将其赋值给 `self`。

第三步是检查 `nil`。如果父类的 `init` 方法因任何原因无法创建请求的对象，它将返回 `nil`。例如，当进程耗尽可用内存且无法分配新对象时，会返回 `nil`。类可以因任何原因决定不构造对象并返回 `nil`。请进行防御性编程；始终假设初始化方法可能返回 `nil`。

如果返回的对象有效，初始化方法应接着执行对象所需的任何初始化。

最后，初始化方法必须向调用者返回其自身，如果初始化失败则返回 `nil`。

研究代码清单 3-17 中的 `-(id)init` 方法。记住它。你将要编写的每一个对象初始化方法都应该与之相似。你无疑会遇到细微的变体——许多程序员将前两个语句合并为 `if ((self=[super init])!=nil)`——但每一个编写良好的 `init` 方法都满足初始化方法的四个部分约定。

#### 链式初始化方法

Java 有用于显式构造函数调用的特殊语法，通过该语法，一个构造函数可以调用带参数的特定父类构造函数（`super(param)`）或另一个构造函数（`this(param)`）。

自然，Objective-C 没有针对此功能的特殊语法，但其原理是相同的。

代码清单 3-18 中所示的 `RepeatableSequence` 类是代码清单 3-17 中 `RandomSequence` 类的子类。`RepeatableSequence` 的 `init` 方法构建在其父类的 `init` 方法以及 `RepeatableSequence` 中的其他方法之上。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**代码清单 3-18. 链式初始化方法**

**Java**

```java
public class RepeatableSequence extends RandomSequence
{
    private long restartSeed;
    public RepeatableSequence()
    {
        this(1);
    }
    public RepeatableSequence( long startingSeed )
    {
        super(startingSeed);
        restartSeed = seed;
    }
    void restartSequence( )
    {
        seed = restartSeed;
    }
}
```

**Objective-C**

```objc
#import "RandomSequence.h"

@interface RepeatableSequence : RandomSequence {
    long long restartSeed;
}
- (id)init;
- (id)initWithSeed:(long long)startingSeed;
- (void)restartSequence;
@end

@implementation RepeatableSequence

- (id)init
{
    self = [self initWithSeed:1];
    return (self);
}

- (id)initWithSeed:(long long)startingSeed
{
    self = [super initWithSeed:startingSeed];
    if (self!=nil) {
        restartSeed = seed;
    }
    return (self);
}

- (void)restartSequence
{
    seed = restartSeed;
}
@end
```

`-(id)initWithSeed:` 方法调用其父类的 `-(id)initWithSeed:` 方法，将初始化参数传递给父类。父类初始化完成后，它再完成自己的初始化。

`-(id)init` 方法将初始化工作移交给 `-(id)initWithSeed:` 方法。请注意，消息 `initWithSeed:` 是发送给其自身，而不是其父类。

#### 指定初始化方法

当子类化一个类时，请阅读该类相关的文档（或注释）。一些 Objective-C 类有一个或多个指定初始化方法。子类的 `init` 方法应仅使用父类的一个指定初始化方法来初始化父类。

许多类的文档都包含一个明确的“子类化说明”部分，其中包含对子类编写者的重要信息和注意事项。

#### 便捷构造函数

类方法的一个常见用途是提供便捷构造函数，有时也称为工厂方法。这些是类方法，它们返回一个同类的预配置对象，理想情况下，使用的代码比正式创建和初始化一个新对象所需的代码更少。代码清单 3-19 显示了 Cocoa 框架提供的 `NSDictionary` 类的一个片段。所有这些类方法都返回一个新的 `NSDictionary` 对象。

**代码清单 3-19. 类便捷构造函数**

```objc
@interface NSDictionary

+ (id)dictionary;
+ (id)dictionaryWithObject:(id)object forKey:(id)key;
+ (id)dictionaryWithDictionary:(NSDictionary *)dict;
+ (id)dictionaryWithObjects:(NSArray *)objects forKeys:(NSArray *)keys;

@end
```

语句 `[NSDictionary dictionary]` 等效于 `[[NSDictionary alloc] init]`，而语句 `[NSDictionary dictionaryWithObject:@"Mary" forKey:@"Name"]` 等效于 `[[NSDictionary alloc] initWithObject:@"Mary" forKey:@"Name"]`。其主要优点是简洁。

■ 注意  
仅当使用垃圾回收时，语句 `[NSDictionary dictionary]` 才等效于 `[[NSDictionary alloc] init]`。在传统的托管内存环境中，这两者完全不同。前者返回一个自动释放的对象，而后者返回一个保留的对象。有关完整说明，请参见第 9 章（关于垃圾回收）和第 24 章（关于内存管理）。

便捷构造函数通常用于构造那些本来难以创建的对象。例如，`NSString` 类提供了 `+(NSString*)pathWithComponents:(NSArray*)components` 便捷构造函数。它接受一个路径组件数组，将它们组合成一个绝对的 POSIX 路径，并将结果作为单个不可变字符串返回。尝试使用 `alloc` 和类的 `init` 方法来完成此操作，需要创建一个可变字符串缓冲区，在循环中追加所有组件，然后将临时字符串缓冲区转换为不可变字符串对象。显然，单条语句 `[NSString pathWithComponents:array]` 要简洁得多。

#### 析构函数

与 Java 类似，Objective-C 对象可以选择性地覆盖其 `-(void)finalize` 方法。此消息在对象被垃圾回收器销毁之前发送给对象。每个 `finalize` 方法必须在返回前立即向其父类发送一条 `finalize` 消息。

代码清单 3-20 显示了一个 `-finalize` 方法的示例。请注意，`finalize` 方法应仅用于异常清理和提供健壮性。编写良好的程序不应依赖 `finalize` 方法来关闭文件；它应该在允许此对象变为可回收之前就关闭文件。`finalize` 代码仅仅是防止在异常情况下泄漏资源，例如，如果文件在程序异常后仍保持打开状态。

**代码清单 3-20. 行为良好的 `finalize` 方法**

```objc
- (void)finalize
{
    if (file!=nil) {
        [file close];
        file = nil;
    }
    [super finalize];
}
```

■ 注意  
`finalize` 消息仅当在垃圾回收环境中运行时才发送给对象。使用托管内存（无垃圾回收）的 Objective-C 应用程序则实现 `-(void)dealloc` 方法。更多详细信息请参阅第 24 章。

#### 缺失了什么？

表 3-2 简要列出了你在 Objective-C 中找不到的 Java 特性。

**表 3-2. 缺失的 Java 特性**

| 特性                | 描述 |
|------------------------|-------------|
| 内部/匿名类 | 你不能在类内部定义类，也不能在方法内部定义类。内部类和匿名类通常用作适配器和代码封装。在 Objective-C 中，这些模式是通过非正式协议和代码块（C 语言的一个近期补充，允许将可执行的代码块作为变量传递）来实现的。 |
| 对象数组          | （原文内容截断） |



