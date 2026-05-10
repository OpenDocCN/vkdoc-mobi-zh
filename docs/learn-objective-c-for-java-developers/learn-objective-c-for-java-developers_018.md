# Objective-C 方法命名与特性

### 方法命名的可读性

**Objective-C** 方法名赋予了代码一种令人耳目一新的冗长性，使其更具自解释性。清单 3-6 展示了两个将子串 `"Walrus"` 提取到字符数组中的代码片段。

**清单 3-6. 方法命名示例**

**Java**

```java
char c[] = new char[20];
String s = "The time has come, the Walrus said ...";
s.getChars(23,29,c,1);
```

**Objective-C**

```objectivec
unichar c[20];
NSString *s = @"The time has come, the Walrus said ...";
[s getCharacters:&c[1] range:NSMakeRange(23,6)];
```

代码短语 `getCharacters:&c[1] range:NSMakeRange(23,6)` 比 `(23,29,c,1)` 更具描述性。很明显，第一个参数涉及字符，第二个参数是一个范围。

这在参数较多的方法中尤为有用。当 **Objective-C** 方法名变得异常长时，格式约定是将每个参数放在单独一行，并水平对齐冒号，如清单 3-7 所示。**Xcode** 编辑器以及其他支持 **Objective-C** 的文本编辑器会自动执行此操作。

**清单 3-7. 多行消息**

```objectivec
NSString *new = [s stringByReplacingOccurrencesOfString:@"Walrus"
                                          withString:@"Carpenter"
                                             options:NSLiteralSearch
                                               range:NSMakeRange(20,10)];
```

### 参数和返回类型

与 **Java** 一样，**Objective-C** 方法可以向调用者返回单个值。到目前为止展示的方法都省略了返回类型和参数类型。这是为了专注于方法声明和命名的基础。参数和返回类型在方法名或参数变量名之前的圆括号中指定，非常类似于类型转换。清单 3-8 展示了一些示例。

**清单 3-8. 参数和返回类型**

```objectivec
- (id)objectForKey:(id)aKey
- (NSMenuItem*)itemWithTag:(NSInteger)aTag;
- (unichar)characterAtIndex:(NSUInteger)index;
- (NSString*)stringByAddingPercentEscapesUsingEncoding:(NSStringEncoding)encoding;
- (void)runInNewThread;
- (void)addAttribute:(NSString*)name value:(id)value range:(NSRange)aRange;
```

如果省略类型，则默认为非特定对象标识符类型（`id`）。声明 `-objectForKey:key` 和 `-(id)objectForKey:(id)key` 是相同的。为了清晰起见，**Objective-C** 程序员始终指定所有参数和返回值的类型，即使类型是 `id`。

参数类型可以是任何有效的 **C** 或 **Objective-C** 变量类型。通常是简单类型，如数字基本类型（`int`）、对象指针（`id` 或 `NSColumn*`）或小型结构体（`NSPoint`）。方法参数和返回值始终通过拷贝传递，因此任何可以使用赋值运算符（`=`）赋值的类型都可以作为参数传递。如前所述，**Objective-C** 不允许声明变量为 **Objective-C** 对象——只能声明指向 **Objective-C** 对象的指针。因此，不能传递 **Objective-C** 对象的副本作为参数，只能传递指向该对象的指针（引用）的副本。因此，不能声明方法 `-(NSString)description`，但可以声明方法 `-(NSString*)description`。

除了有效的变量类型外，方法的返回类型也可以是 `(void)`，表示它不返回任何值。如果尝试使用 `void` 方法的返回值，编译器会发出警告。如果尝试从 `void` 方法返回值，或者未能从非 `void` 方法返回值，编译器也会发出警告。

### 方法选择器

一个 **Objective-C** 方法的完整名称（不含参数变量名和任何类型）构成了其唯一标识符。方法 `-(NSFont*)fontWithFamily:(NSString*)family traits:(NSFontTraitMask)fontTraitMask weight:(NSInteger)weight size:(CGFloat)size` 的标识符是 `fontWithFamily:traits:weight:size:`。这相当于 **Java** 中的方法签名。该标识符的具体体现是一个称为 **选择器（selector）** 的数值常量。选择器是类型 `SEL` 的值。

**Objective-C** 指令 `@selector()` 返回括号内方法标识符的选择器值，例如 `SEL selector = @selector(fontWithFamily:traits:weight:size:)`。

> **注意**：人们很容易将方法名中的关键字视为参数名，但这在技术上并不准确。与具有命名参数的语言不同，**Objective-C** 方法的参数不能省略或重新排列。方法 `-sendMessage:toRecipient:withAttachement:` 和 `-sendMessage:withAttachement:toRecipient:` 是具有唯一签名的不同方法。

选择器在内部用于向对象分发消息。它们可以在编程中用于发送消息、注册消息、执行内省以及后续章节描述的其他技巧。

在文档和程序员之间的交流中，方法名通常像调用一样书写，包括类名和方法标识符。程序员在引用 `NSFontManager` 类定义的实例方法 `fontWithFamily:traits:weight:size:` 时，可能会写成 `-[NSFontManager fontWithFamily:traits:weight:size:]`。这是无意义的代码，但简洁地描述了该方法。

### 实例变量

实例变量在类的 `@interface` 指令中声明，如清单 3-1 所示。这与 **Java** 几乎相同，唯一的微小限制是所有实例变量声明必须组合在一个单独的块中。与 **Java** 一样，实例方法访问实例变量就像访问局部变量一样。

> **注意**：实例变量和方法名称传统上使用“驼峰命名法”——它们以小写字母开头，并使用大写字母分隔单词。以 `_`（下划线）开头的实例变量和方法名称由 **Cocoa** 框架保留，应避免使用。

你也看到了如何使用间接成员运算符直接访问另一个对象中的成员变量，例如 `object->value = 1`（实际上应写为 `object->value = 1`，而非 `object.value = 1`，后者是 **Java** 语法）。在 **Java** 中通常不会这样做。良好的 **Java** 实践鼓励使用访问器（getter）和修改器（setter）方法，以将变量的实现与外部代码隔离。实例变量应声明为 `protected` 或 `private`，并使用 `int getValue()` 和 `void setValue( int value )` 方法来获取和设置其值。

良好的 **Objective-C** 实践遵循与 **Java** 相同的原则，原因与 **Java** 相同，甚至更多。在 **Objective-C** 中，实例变量 `int value` 可通过 `-(int)value` 和 `-(void)setValue:(int)newValue` 方法访问。**Objective-C** 非常鼓励使用访问器方法，以至于访问器方法的定义和构造已以内置属性的形式融入语言中。如何声明属性将在关于 `isa` 变量的章节之后立即描述。

> **注意**：**Objective-C** 使用名称 `value` 和 `setValue` 作为获取和设置属性值的方法，而不是 **Java** 中更常见的 `getValue` 和 `setValue`。使用内省通过访问器方法识别属性的技术通常接受 `getValue` 形式作为替代，但首选 `value` 形式作为 getter。

### `isa`

**Objective-C** 中的每个对象都继承了一个 `isa` 实例变量；`isa` 字面上定义了对象的类。



### 属性

在运行时，每个类都有且只有一个`Class`对象的实例。`Class`对象定义了该类所有实例的行为。该类中的每个实例都通过其`isa`变量引用其所属的`Class`对象。

你不应直接访问对象的`isa`变量。若要获取对象的`Class`，`-(Class)class`方法会返回它。你还可以使用`-(NSString*)className`方法：该方法以字符串形式返回对象所属类的名称。

某些技术会修改对象的`isa`变量（这种技术称为“isa 混写”）以动态改变该对象的行为。在未清晰且深入理解 Objective-C 运行时架构之前，切勿自行更改对象`isa`变量的值。

### 属性

Objective-C 2.0 新增了`@property`和`@synthesize`关键字，用于定义对象属性并实现其对应的访问器方法。属性是一种通过访问器方法获取、通过设值器方法设置的值。属性的值通常存储在实例变量中，但这并非必需。

`@property`指令用于声明类的属性。`@property`指令通常出现在`@interface`指令中。它既不会实现访问器方法，也不会创建任何实例变量。它仅仅是一个承诺，表明该类实现了该属性。清单 3-9 展示了分别用 Java 和 Objective-C 编写的`Person`类。该类定义了五个属性：`tag`、`firstName`、`lastName`、`fullName`和`adult`。

### 清单 3-9\. 属性声明

**Java**

```java
public class Person
{
    int tag;
    String firstName;
    String secondName;
    boolean adult;

    synchronized int getTag()
    {
        return (tag);
    }

    synchronized void setTag( int tag )
    {
        this.tag = tag;
    }

    public synchronized String getFirstName()
    {
        return firstName;
    }

    public synchronized void setFirstName( String firstName )
    {
        this.firstName = firstName;
    }

    public synchronized String getLastName()
    {
        return secondName;
    }

    public synchronized void setLastName( String lastName )
    {
        secondName = lastName;
    }

    public synchronized boolean isAdult()
    {
        return adult;
    }

    public synchronized void setAdult( boolean adult )
    {
        this.adult = adult;
    }

    public String getFullName()
    {
        return (firstName+" "+secondName);
    }
}
```

**Objective-C**

```objectivec
@interface Person : NSObject {
    int tag;
    NSString *firstName;
    NSString *secondName;
    BOOL adult;
}

@property int tag;
@property (copy) NSString *firstName;
@property (copy) NSString *lastName;
@property (getter=isAdult) BOOL adult;
@property (readonly,nonatomic) NSString *fullName;

@end

@implementation Person

@synthesize tag;
@synthesize firstName;
@synthesize lastName = secondName;
@synthesize adult;

- (NSString *)fullName
{
    return ([NSString stringWithFormat:@"%@ %@",firstName,secondName]);
}

@end
```

`@synthesize`指令出现在类的`@implementation`中。`@synthesize`指令会告诉编译器生成实现属性所需的访问器和设值器方法。

如果缺少`@synthesize`指令，则需要由程序员来实现满足`@property`指令中声明的约定的方法。如果既未包含`@synthesize`指令，也未实现预期的访问器方法，则视为错误。

> **注意：** 特殊的`@dynamic`指令可以抑制编译器对所需访问器方法的预期。在某些情况下（例如，类扩展、分类、子类化、动态加载的框架），类可能在运行时实现编译器未知的方法。`@dynamic`指令告知编译器无需为此担心；程序员承诺程序执行时预期的方法将会存在。很少使用`@dynamic`。

前两个属性较为简单。`@property int tag`声明了`Person`类实现了满足名为`tag`的整型属性契约的 getter 和 setter 方法。



