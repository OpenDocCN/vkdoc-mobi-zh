# 第三章：欢迎使用 Objective-C

`@synthesize` 标签指令会指示编译器为你生成 `-(int)tag` 和 `-(void)setTag:(int)newTag` 方法。`tag` 属性将存储在同名的实例变量中。

第二个属性声明与之类似，但由于该属性是一个对象，我们必须包含一个属性特性来阐明如何处理赋值操作。`copy` 特性会使合成后的设置器创建并存储传递给它的对象副本（而非存储对该对象的引用）。与 `tag` 类似，`firstName` 属性的值将存储在 `firstName` 实例变量中。

第三个 `@property` 指令与第二个相同，只是属性名称与任何已声明的实例变量都不对应。这对 `@property` 指令无关紧要；该语句仅承诺该类将实现该属性，而不承诺如何实现。`@synthesize lastName = secondName` 指令使用 `secondName` 实例变量来存储，并为 `lastName` 属性实现所需的 getter 和 setter 方法。获取 `lastName` 属性会返回 `secondName` 变量；设置 `lastName` 属性会用传递给 `-setLastName:` 的值的副本替换 `secondName` 变量。

> **提示：** 如果你更喜欢极其紧凑的代码，清单 3-9 中的四条 `@synthesize` 指令本可以写成 `@synthesize tag, firstName, lastName=secondName, adult`。

`@property (getter=isAdult) BOOL adult` 指令声明了一个名为 `adult` 的布尔属性。`getter=` 特性将 getter 方法重命名为 `-(BOOL)isAdult`，而不是默认的 `-(BOOL)adult`。与 Java 中一样，布尔属性的 getter 方法传统上以“is”或“has”开头。

最后一个 `@property (readonly,nonatomic) NSString *fullName` 指令声明 `Person` 对象提供了一个名为 `fullName` 的字符串属性。该属性是只读的（它有 getter 方法但没有 setter 方法），并且 getter 方法不是线程安全的。在该类的 `@implementation` 部分中没有针对 `fullName` 的 `@synthesize` 指令。所需的 getter 方法由程序员实现。

> **警告：** 在实现访问器方法以满足 `@property` 指令时，程序员有责任履行声明中隐含的契约。如果你省略了 `nonatomic` 特性，则需要你自行确保这些方法是线程安全的。如果指定了 `copy` 特性，setter 方法应创建并存储值的副本，而不是指向值的引用（除非你能保证该值是不可变的）。

#### 属性特性

清单 3-9 中的示例展示了属性定义中使用的一些常见特性。表 3-1 描述了所有可以包含在 `@property` 指令中的属性特性。多个特性之间用逗号分隔。

**表 3-1. 属性特性**

| 属性特性 | 描述 |
| --- | --- |
| `readonly` | 声明该属性是不可变的。针对 `readonly` 属性的 `@synthesize` 指令将生成一个 getter 方法，但不会生成 setter 方法。`readonly` 与 `readwrite` 互斥。 |
| `readwrite` | 声明该属性是可变的。这是默认的可变性特性。如果既未指定 `readonly` 也未指定 `readwrite`，则假定为 `readwrite`。`readwrite` 与 `readonly` 互斥。 |
| `copy` | 声明 setter 方法会创建传递给它的对象的副本，并存储对该副本的引用，而不是对原始对象的引用。此特性仅适用于对象指针属性——原始值总是通过复制传递。`copy` 与 `assign` 和 `retain` 互斥。当用于设置属性的对象可能可变，且你不希望因原始对象被修改而导致接收者属性的值发生变化时，请使用 `copy`。 |
| `assign` | 声明 setter 方法通过保留指向传递给它的对象的指针来设置属性。实际上，这意味着 getter 和 setter 方法是通过简单的赋值来实现的（例如，`self->firstName = name`）。在垃圾回收环境中，当你希望保留对原始对象值的引用，或者对象值始终是不可变时，请使用 `assign`。如果未指定存储特性，通常假定为 `assign`，但在某些环境中，你必须显式包含 `assign`、`copy` 或 `retain`。`assign` 与 `copy` 和 `retain` 互斥，并且仅适用于对象指针属性。 |
| `retain` | 此特性类似于 `assign`，但在托管内存（非垃圾回收）环境中会保留对对象的引用。有关非 GC 内存管理的信息，请参考第 4 部分。 |
| `getter=名称` | 重命名用作 getter 方法的方法。当需要默认 getter 方法名称的替代形式（例如 `-isValue` 或 `-getValue`）时非常有用。请谨慎使用此特性；它可能会破坏 getter 和 setter 名称的标准模式，导致键值编码等技术无法识别该属性。 |
| `setter=名称` | 重命名用于 setter 方法的方法。如果指定了 `readonly` 特性，则不能使用。适用于 `getter=` 的相同警告也适用于 `setter=`。 |
| `nonatomic` | 声明 getter 和 setter 方法不是线程安全的。不存在“atomic”特性；省略 `nonatomic` 意味着该属性是原子的。如果省略，`@synthesize` 可能会用代码包裹 getter 和 setter 方法，这些代码首先获取对象特定的锁，以确保访问器方法在多线程环境中行为良好。它还能确保返回的对象在执行线程的内存空间中是完全实现的。当你手动编写的访问器方法不提供这些安全保障，或不希望采取此类预防措施时，请使用 `nonatomic`。线程安全特性会给访问器方法增加相当大的运行时开销，这可能会影响性能。在某些平台上，对于原始标量属性和 `assign` 对象属性存在例外情况，因为处理器保证了原子赋值。 |

与 Java 相比，`copy` 特性更适用于字符串和集合对象。在 Objective-C 中，字符串和集合类具有可变子类。因此，`NSString` 不能像 Java 的 `String` 对象那样保证是不可变的。制作副本可以确保属性不会受到用于设置属性的原始对象更改的影响。

#### 重写属性

属性是通过在接收者中获取和设置值的访问器方法实现的。由于它们是使用方法实现的，子类可以重写这些方法，本质上就是重写属性。

非正式或正式地重写属性的访问器方法都是可以的。要非正式地重写，只需重写相应的访问器方法即可。要重写由 `@property int tag` 隐含的 setter 方法，只需在子类中实现一个新的 `-(void)setTag:(int)newTag` 方法。甚至不必在子类的 `@interface` 中声明它。

要正式地重写属性，请在子类中声明一个重复的 `@property` 指令。`@property` 指令必须与父类中的完全相同，但有一个例外：当父类的属性是 `readonly` 时，子类可以将其声明为 `readwrite`。这支持了不可变父类的可变子类的设计模式。一旦声明，就可以使用 `@synthesize` 指令为子类重新实现访问器方法，或者你也可以自己实现它们。



### 格式规范说明

本文档遵循标准 Markdown 语法，对原文进行了排版优化：调整了标题层级、删除了多余空行、保留了核心内容，并对代码、变量、类名等元素添加了反引号。代码块使用三个反引号包裹。

---

### 提示
这是 `@dynamic` 指令的一个实际应用。由于父类已经实现了属性的访问器方法，子类可以重新声明该属性，并使用 `@dynamic` 指令来忽略重新实现两个访问器方法的要求，然后仅重写 `getter` 或 `setter` 方法。

### 访问属性

属性的访问器方法可以像其他任何方法一样被调用。但正式声明的属性还有一个额外的好处。Objective-C 2.0 扩展了成员变量运算符（`.`），以允许轻松访问正式定义的属性。这种所谓的“点语法”让你能够像在 Java 中访问成员变量一样，与对象的属性进行交互。

使用清单 3-9 中定义的类，清单 3-10 中的代码设置并访问了一个 `Person` 对象的多个属性。编译器会将点语法扩展为调用每个属性对应的 `getter` 或 `setter` 方法；而不是像 Java 那样直接访问对象的实例变量。这被视为“语法糖”，旨在提高可读性并减少杂乱。清单 3-10 生成的代码与清单 3-11 所示的代码完全相同。

**清单 3-10. 通过点语法访问对象属性**

```objectivec
Person *person = ...;

person.firstName = @"James";

if (person.lastName.length==0)

person.lastName = @"Smith";

person.tag += 3;
```

> [www.it-ebooks.info](http://www.it-ebooks.info/)

**第 3 章 ■ 欢迎来到 Objective-C**

**清单 3-11. 点语法的等效代码**

```objectivec
Person *person = ...;

[person setFirstName:@"James"];

if ([[person lastName] length]==0)

[person setLastName:@"Smith"];

[person setTag:[person tag]+3];
```

### 作用域

我先说一个坏消息：Objective-C 中没有包的概念。所有类名、C 函数和全局变量共享一个名称空间。类内部的实例变量由该类封装，并且 Objective-C 确实提供了对其作用域的控制。Objective-C 不限制对类方法的访问，但有一些实用的方法可以模拟你在 Java 中享受的那种访问限制。

#### 类名作用域

类名都是公开的，并且存在于同一个全局可访问的名称空间中。为了避免命名冲突，Objective-C 开发者采用了一种命名约定，以便类能够与其他类共存。逻辑组中的类都以通用的两个字符缩写开头。在 Apple 的框架中，所有 Core Image 类都以 `CI` 开头（`CIColor`、`CIFilterShape`、`CIVector`……）。QuickTime 的类以 `QT` 开头（`QTTrack`、`QTMovie`、`QTTimeValue`……）。

> **注意** 如果你好奇的话，Cocoa 基础类中出现的 `NS` 前缀代表 NextStep。NextStep 是由 NeXT Computer 开发的 Objective-C 操作系统。当 Apple 在 1996 年收购 NeXT 时，将 NextStep 采用为新兴 Mac OS X 的核心技术。在乔治·奥威尔会钦佩的壮举中，几乎所有对 NeXT 和 NextStep 的引用都从 Mac OS X 的源代码和文档中被删除了。但是要重命名这个庞大框架中的每一个类几乎是不可能的，因此遗留的 `NS` 前缀一直沿用至今。

由第三方开发的类遵循同样的约定。OmniGroup 提供了一套广泛的应用程序类供其他开发者使用。`OmniAppKit` 类都以 `OA` 开头（`OAController`、`OAUtilities`、`OAScriptMenuItem`……）。`OmniNetworking` 类都以 `ON` 开头（`ONHost`、`ONPortAddress`……）。

在你自己的开发中，需要考虑你所创建类的作用域。如果该类仅在你的应用程序开发环境中使用，则无需担心前缀。给你的类起简单的名字，比如 `Student`、`BoardGame`、`Troll` 等。如果你正在开发供其他程序员使用的类，即使仅限于组织内部，也要选择一个可能唯一的前缀，并相应地命名你的类。如果你的公司是 Widget Makers，你可以将类命名为 `WMToy`、`WMAlarm` 等。你也不必现在就做出决定。Xcode IDE 包含重构工具，使得重命名类相对容易。

> [www.it-ebooks.info](http://www.it-ebooks.info/)

**第 3 章 ■ 欢迎来到 Objective-C**

#### 实例变量作用域

对实例变量的访问控制方式与 Java 相同，并且很方便地使用了相同的术语。Objective-C 没有单独指定每个变量的作用域，而是采用了 C++ 风格的作用域指令。在 `@interface` 指令的实例变量块中，可以在任何变量声明之前使用 `@public`、`@protected` 和 `@private` 指令。在某个作用域指令之后声明的所有变量都继承指定的可见性。清单 3-12 显示了四个具有不同可见性级别的实例变量。

**清单 3-12. 实例变量作用域**

```objectivec
@interface Toy : NSObject {

NSString *name;

@public

int starRating;

@protected

NSRange recommendedAge;

int playCount;

@private

NSSet *toyBox;

}

@end
```

变量 `starRating` 是公开的。它可以被任何上下文中的任何代码访问。

变量 `name`、`recommendedAge` 和 `playCount` 的作用域是受保护的。定义在此类或其任何子类中的方法可以直接访问这些变量。`@protected` 是实例变量的默认作用域。在第一个作用域指令之前或没有任何作用域指令的情况下定义的变量都是受保护的。

变量 `toyBox` 是私有的。它只能被此类中定义的方法访问。

请记住，Objective-C 的变量作用域仅仅是阻止对实例变量的访问。如果你尝试在无权访问的上下文中使用成员变量，编译器会报错。仍然可以通过内省或强制转换指针来访问这些变量——Objective-C 没有 Java 意义上的“安全性”。此外，变量名称在技术上并不像 Java 那样在类的名称空间内进行作用域限定。`toyBox` 实例变量对于 `Toy` 类的所有子类都存在。`Toy` 的子类不能声明自己的实例变量 `toyBox`；编译器会发出重复成员的错误。

#### 方法作用域

方法始终是公开的，就 Java 意义上的公开而言。Objective-C 对谁可以调用指向该对象的方法没有运行时限制。虽然没有明确的方法来限制方法被调用，但有几种技术可以隐藏方法，以阻止在其预期作用域之外使用。

最简单的技术是将方法包含在 `@implementation` 部分，但从 `@interface` 指令中省略方法原型。其他 `#import` 该类接口的模块将不知道该方法的存在，如果尝试发送该消息，编译器会报错。

这种技术之所以有效，是因为 `@implementation` 部分中的方法隐含了自身的原型。一旦方法出现在实现中，编译器就知道该方法，并允许你像在类的接口中预定义它一样调用它。这种技术是随意且简单的。它最大的缺点是所有未声明的方法必须在被引用之前定义。这可能会使源代码组织变得尴尬。在某些递归情况下，这使得它不可能实现。

> [www.it-ebooks.info](http://www.it-ebooks.info/)

**第 3 章 ■ 欢迎来到 Objective-C**



#### 类似的技术也适用于 `@property` 指令。

之前我谨慎地提到“一个`@property`指令通常出现在`@interface`指令中”。`@property`指令也可以出现在`@implementation`中。其含义相同，但从`@interface`中省略它也会对其他模块隐藏该定义。

Objective-C 的类别和扩展是细分类声明的更正式模式，允许你将关于类的知识进行分类整理。这些技术以及如何利用它们来模拟 Java 的 private 和 protected 方法，将在后续章节中解释。

最后，创建一个“辅助”类有时也很有用——这种类的目的完全服务于另一个类的内部实现。一个辅助类可以在使用它的类的实现文件中完全声明和实现。辅助类的接口和实现对外部类都是隐藏的。如果该类需要一个指向辅助对象的实例变量，请使用一个`@class`指令（接下来会介绍）或 `id` 类型。

#### 前向 `@class` 指令

`@class` 指令声明了一个类名，而没有定义该类。它允许你声明一个对类的引用，而无需编译该类的`@interface`。在 `@class` 指令之后，你可以引用这个类，但不能编译任何假设了解该类信息的代码——因为编译器对此一无所知。在该类的实际 `@interface` 指令被编译之前，你无法向该类的对象发送消息，也无法访问它的任何属性。

一旦你开始声明指向对象的指针变量，你就会发现编译器要求这些类先被定义，然后才允许你在语句中使用该类名。很自然的倾向是为你 `@interface` 指令中引用的每个对象 `#import` 所需的类头文件。对于较小的项目来说，这没问题。

对于大型项目，这会给编译器带来负担（翻译过来就是：它会拖慢你的开发速度）。每个使用类 A 的类都会导入其接口（`#import "A.h"`）。如果类 A 拥有指向类 B、C、D 对象的实例变量，它就会导入所有这些头文件。如果类 B、C、D 又共同包含了对类 E 到 N 的引用，它们的头文件就会导入所有这些头文件，依此类推。随着项目复杂度的增长，编译每个模块所需的工作量也会呈几何级数增长。

`@class` 指令可以显著减轻这一负担。一个 `@interface` 指令通常只需要知道一个被引用的类存在即可。`@class` 指令的一个典型应用如代码清单 3-13 所示。

**代码清单 3-13.** 前向 `@class` 指令

```
#import "Vehicle.h"

@class Engine;
@class MoonRoof;
@class Radio;

@interface Car : Vehicle {
    Engine *engine;
    MoonRoof *moonRoof;
    Radio *radio;
}

- (void)startEngine;
- (void)stopEngine;
- (void)tuneRadio;

@end
```

`Car` 类包含了指向 `Engine`、`MoonRoof` 和 `Radio` 对象的引用。一个导入了此 `@interface` 的模块可以使用 `Car` 的方法和属性，但对其他对象一无所知。`Car` 的 `@implementation`（大概会使用到这些其他对象）则以导入这些类的完整定义开始。

作为规则，我会 `#import` 父类、所有公共对象的类以及方法返回的对象类（假设调用者会使用返回的对象）的头文件。`@interface` 中所有剩下的类都使用 `@class` 声明。

`@class` 指令也使得声明具有循环引用的类成为可能。换句话说，类 A 引用了类 B，而类 B 又引用了类 A。

#### `self` 和 `super`

Objective-C 方法有两个预定义的变量用于引用自身：`self`（this）和 `super`（super）。它们的工作原理与 Java 中的对应物完全相同，如代码清单 3-14 所示。

**代码清单 3-14.** `self` 和 `super` 的使用

```
#import "Person.h"

@interface Voter : Person {
}

- (void)setAdult:(BOOL)isAdult;

@end

@implementation Voter

- (void)setAdult:(BOOL)isAdult
{
    if (isAdult && !self.isAdult)
        [VoterRegistration registerVoter:self];
    [super setAdult:isAdult];
}

@end
```

`self` 变量始终是指向接收者（其方法被调用的那个对象）的指针。`self` 变量的类型是指向实现了该方法的类的指针。它可用于访问该类定义或继承的实例变量（例如 `self->secondName`），但不能访问子类的变量（即使对象的实际类是子类）。

> **注意** Java 程序员最初可能会觉得这很奇怪，但 `self` 是一个可修改的自动变量，可以在方法体中重新赋值。我稍后会解释为什么需要这样做。现在，只需要注意不要无意中更改它即可。

语句 `[self method]` 向自身发送一个消息。等效的 Java 语句是 `this.method()` 或仅仅是 `method()`。伪变量 `super` 与 `self` 相同，但其唯一实际用途是发送消息。语句 `[super method]` 调用由对象父类定义的方法，等效于 Java 语句 `super.method()`。

### 类方法

到目前为止，我们只为类定义了实例方法。回顾一下，实例方法以减号（`-`）前缀声明。它定义了该类实例将响应的消息。

以加号（`+`）开头的方法名定义了一个类方法。正如在 `isa` 部分所述，每个类在运行时创建了一个单一的 Class 对象，该对象定义了该类所有实例的身份和行为。类方法定义了该单一 Class 对象将响应的消息。类方法（从技术上讲）并不等同于 Java 中的静态方法，尽管它们倾向于扮演相同的角色。

在表达式中使用类名时，它求值为该类的单例 Class 对象。要调用一个类方法，请使用类名作为接收者，如代码清单 3-15 所示。使用类名会将消息发送给该类的 Class 对象。

**代码清单 3-15.** 类方法和实例方法的调用

```
@interface RandomSequence : NSObject {
    long long int seed;
}

+ (NSNumber *)number;
+ (NSString *)string;

- (NSNumber *)nextNumber;
- (NSString *)string;

@end

...

NSNumber *n = [RandomSequence number];
NSString *s = [RandomSequence string];

RandomSequence *r = ...;
n = [r nextNumber];
s = [r string];
```

> **注意** 按照惯例，类方法首先定义，随后是实例方法。

方法 `+(NSNumber*)number` 定义了一个返回 `NSNumber` 对象的类方法。语句 `[RandomSequence number]` 将消息 `number` 发送给定义 `RandomSequence` 类的单一 Class 对象。

一个 `string` 方法同时为 Class 对象和类的实例定义了。语句 `[RandomSequence string]` 将消息 `string` 发送给 Class 对象，并执行 `+(NString*)string` 中的代码。语句 `[r string]` 将消息 `string` 发送给 `RandomSequence` 类的一个实例，并执行 `-(NSString*)string` 中的代码。这里没有歧义或命名冲突，因为 `-string` 和 `+string` 是为不同对象实现的。



