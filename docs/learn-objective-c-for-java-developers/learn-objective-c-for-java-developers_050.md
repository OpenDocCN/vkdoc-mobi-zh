# 异常处理

这严格用于调试，并允许你正常启动应用程序。如果未捕获的异常导致应用程序挂起，你仍然可以附加调试器到进程以调查原因，因为该进程（从技术上讲）仍在运行。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 14 章 异常处理

### 表 14-2. 进程挂起条件标志

| 常量 | 效果 |
|------|------|
| `NSHangOnUncaughtExceptionMask` | 如果遇到未捕获的异常，挂起进程。 |
| `NSHangOnUncaughtSystemExceptionMask` | 如果发生系统事件，挂起进程。 |
| `NSHangOnUncaughtRuntimeErrorMask` | 如果发生运行时事件，挂起进程。 |
| `NSHangOnTopLevelExceptionMask` | 如果主运行循环遇到未捕获的异常，挂起进程。 |
| `NSHangOnOtherExceptionMask` | 如果抛出任何其他类型的异常，挂起进程。 |

清单 14-5 展示了一个应用程序的初始化代码，该程序希望记录所有未捕获的异常、系统事件和运行时事件。

### 清单 14-5. 初始化未捕获异常处理

```objectivec
#import <ExceptionHandling/NSExceptionHandler.h>

@implementation MyApplicationDelegate

- (void)applicationWillFinishLaunching:(NSNotification*)notification
{
    // Log all uncaught exceptions, but not low-level exceptions
    [[NSExceptionHandler defaultExceptionHandler] setExceptionHandlingMask:
        NSLogUncaughtExceptionMask
        |NSLogUncaughtSystemExceptionMask
        |NSLogUncaughtRuntimeErrorMask
        |NSLogTopLevelExceptionMask
        /*|NSLogOtherExceptionMask*/
    ];
}

@end
```

### 遗留异常

Objective-C 中的异常语法相对较新。在它被添加之前，Objective-C 语言没有直接支持异常。相反，异常处理由一组类和巧妙的预处理器宏提供。清单 14-6 展示了遗留异常处理的样子。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 14 章 异常处理

### 清单 14-6. 遗留异常处理

```objectivec
NS_DURING
    NSLog(@"%s trying",__func__);
    [self thrower];
NS_HANDLER
    NSLog(@"caught NSException: %@",localException);
    [localException raise];
NS_ENDHANDLER
```

遗留的`NS_DURING`、`NS_HANDLER`和`NS_ENDHANDLER`预处理器宏展开为使用 C 语言`longjmp(...)`函数创建必要执行流程控制的 C 代码。只有一个处理程序块，它捕获所有异常。变量`localException`包含捕获的异常。没有`finally`块。通过向`NSException`对象发送`-raise`消息来抛出异常。

表 14-3 展示了遗留异常语法及其现代等价物。

### 表 14-3. 遗留和现代异常语法

| 遗留语法 | 等价现代语法 |
|---------|-------------|
| `NS_DURING` | `@try` |
| `{` | `{` |
| `NS_HANDLER` | `} @catch (NSException *localException) {` |
| `NS_ENDHANDLER` | `}` |
| `[exception raise]` | `@throw exception` |
| `NS_VALUERETURN(value,type)` | `return (value)` |
| `NS_VOIDRETURN` | `return` |

如果你想要从`NS_DURING`和`NS_HANDLER`之间的代码块返回，则必须使用`NS_...RETURN`宏。

如果你的 Objective-C 编译器启用了现代异常处理（很可能如此），表 14-3 中的预处理器宏将展开为现代语法。因此，对于为遗留异常编写的代码来说，没有惩罚或不兼容性，你可以自由地混合使用两者。

一个小的区别是：`-raise`方法执行`@throw`，但现代的`@throw`指令不会向被抛出的对象发送`-raise`消息。这可能对已覆写`-raise`的子类有影响。

### 断言

断言是一种编程技术，允许你定义自己的运行时异常。断言是一条语句，它确认（断言）程序员期望为真的条件确实为真。如果该语句被发现为假，则会抛出一个`NSInternalInconsistencyException`异常。清单 14-7 中的断言确保从集合中获取的对象的类是预期的类型。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 14 章 异常处理



## 使用断言确认类成员身份

`NSDictionary *dictionary = …`  
`NSNumber *value = [dictionary objectForKey:@"Value"];`  
`NSAssert([value isKindOfClass:[NSNumber class]], @"wrong class");`

失败断言输出：

```
*** Assertion failure in -[Tosser catcher], Tosser.m:45
*** Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'wrong class'
```

断言是将 Java 的一些严谨性引入 Objective-C 的实用方法。Objective-C 在赋值时不会测试对象的类，但断言可以做到。Objective-C 不会检查 C 数组语句中的索引是否越界，但断言可以做到。简而言之，几乎所有你期望 Java 为你执行的运行时检查，都可以通过断言来表达。

断言可以用于任何预期的程序条件。如果某个参数应为非 `nil`，请断言它；如果某个整数值应为正数，请断言它。

> **注意**：断言也是异常，与其他异常类似。大多数 Java 运行时异常是未检查的，并且通常不被捕获。如果你希望在遇到异常时应用程序终止，请小心不要在 catch 块中吞掉异常。请参见清单 14-8 中的 `RethrowAssertion` 宏。

断言一个特别吸引人的特性是，它们可以统一关闭。Cocoa 框架提供的断言宏（见表 14-4）都基于 `NS_BLOCK_ASSERTIONS` 预处理器宏进行条件定义。如果定义了该宏（值无关紧要），所有 `NSAssert…` 宏都会被重新定义为空。这意味着你的断言语句实际上会消失，仿佛从未存在过。你可以在应用程序中填充数千条在开发期间活跃的断言语句，然后在编译发布版本时将它们全部关闭。这通常通过在 Release 配置的 Preprocessor Macros 构建设置中定义 `NS_BLOCK_ASSERTIONS` 宏来实现。在开发期间，你的应用程序会检查任何意外条件，而发布版本则没有多余的代码和每个断言的性能开销。

**表 14-4. 标准断言宏**

| 宏 | 描述 |
|---|---|
| `NSAssert(condition, description)` | 测试一个条件，如果为 false，则抛出带有描述的异常。 |
| `NSAssert1(condition, format, arg1)` | 如果条件为 false，则通过使用一个参数格式化字符串来创建描述，并抛出异常。 |
| `NSAssert2(condition, format, arg1, arg2)` | 如果条件为 false，则通过使用两个参数格式化字符串来创建描述，并抛出异常。 |
| `NSParameterAssert(condition)` | 如果条件为 false，则抛出带有描述（给定条件不成立）的异常。 |

实际上有六个 `NSAssert` 宏：`NSAssert`、`NSAssert1`、`NSAssert2`、`NSAssert3`、`NSAssert4` 和 `NSAssert5`。它们仅在格式字符串后的参数数量上有所不同。（预处理器宏不支持可变参数。）这些断言只能在 Objective-C 方法的主体中使用。一组并行的 `NSCAssert…` 宏可用于 C 函数。

我强烈建议定义你自己的断言宏。简洁、具体的断言语句更有可能被使用。清单 14-8 分享了我最喜欢的一些自定义断言宏以及使用它们的代码。

## 清单 14-8. 自定义断言宏

```
#if !defined(NS_BLOCK_ASSERTIONS)

#define RethrowAssertion(EXCEPTION) \
if ([[EXCEPTION name] isEqualToString:NSInternalInconsistencyException]) \
    [EXCEPTION raise]

#define AssertObjectIsClass(OBJECT,CLASS) \
do { \
    if (![OBJECT isKindOfClass:[CLASS class]]) { \
        [[NSAssertionHandler currentHandler] handleFailureInMethod:_cmd \
            object:self \
            file:[NSString stringWithUTF8String:__FILE__] \
            lineNumber:__LINE__ \
            description:@"object isa %@@%p; expected %s", \
            [OBJECT className],OBJECT,#CLASS]; \
    } \
} while(NO)

#define AssertObjectIsNilOrClass(OBJECT,CLASS) \
do { \
    if ((OBJECT!=nil) && ![OBJECT isKindOfClass:[CLASS class]]) { \
        [[NSAssertionHandler currentHandler] handleFailureInMethod:_cmd \
            object:self \
            file:[NSString stringWithUTF8String:__FILE__] \
            lineNumber:__LINE__ \
            description:@"object isa %@@%p; expected %s", \
            [OBJECT className],OBJECT,#CLASS]; \
    } \
} while(NO)

#define AssertObjectRespondsTo(OBJECT,MESSAGE) \
do { \
    if (![OBJECT respondsToSelector:@selector(MESSAGE)]) { \
        [[NSAssertionHandler currentHandler] handleFailureInMethod:_cmd \
            object:self \
            file:[NSString stringWithUTF8String:__FILE__] \
            lineNumber:__LINE__ \
            description:@"object %@@%p does not respond to %s", \
            [OBJECT className],OBJECT,#MESSAGE]; \
    } \
} while(NO)

#define AssertNotNil(OBJ) NSAssert1(OBJ!=nil,@"%s is nil",#OBJ)
#define ParameterAssert NSParameterAssert

#else

#define RethrowAssertion(EXCEPTION)
#define AssertObjectIsClass(OBJECT,CLASS)
#define AssertObjectIsNilOrClass(OBJECT,CLASS)
#define AssertObjectRespondsTo(OBJECT,MESSAGE)
#define AssertNotNil(OBJ)
#define ParameterAssert NSParameterAssert

#endif

// 确保委托已设置并实现了非正式协议
AssertNotNil(delegate);
AssertObjectRespondsTo(delegate,lookupPartNumber:);
PartNumber part = [delegate lookupPartNumber:inventoryCode];
NSAssert(part>=100000&&part<=999999,@"invalid part number");
Record *partRecord = [PartsDatabase recordForPartNumber:part];
AssertObjectIsClass(partRecord,Record);
```

你可以用其他宏来定义这些宏——比如 `AssertNotNil` 和 `ParameterAssert` 宏——或者将它们定义为调用单例 `NSAssertionHandler`，该处理程序用于抛出断言异常。在设计宏时，始终确保它们在被展开时像一个单一的 C 语句一样工作。

这些宏使用了多个高级预处理器特性：

-   行尾的单个 `\` 将 `#define` 语句延续到下一行，就像它被写在一行上一样。
-   宏可以采取带参数的 C 函数形式。这些参数创建了在宏主体展开期间存在的临时宏定义。语句 `#define DO(THIS) [self THIS]` 会将代码 `DO(something)` 替换为 `[self something]`。
-   宏展开是递归的。给定 `#define A B` 和 `#define B(X) return X`，语句 `A(3)` 会被替换为 `B(3)`，然后再替换为 `return 3`。定义顺序并不重要。
-   宏文本中的特殊语法 `#TOKEN` 会被替换为包含 `TOKEN` 文本的 C 字符串常量。`#define RETURN_STR(S) return (#S)` 会将源代码 `RETURN_STR(100-1)` 替换为 `return ("100-1")`。这对于将条件语句之类的东西转换为描述条件的字符串特别方便。
-   特殊宏 `__FILE__` 和 `__LINE__` 由编译器生成，计算结果为正在编译的源文件的文件名和行号。`__func__`、`__DATE__` 和 `__TIME__` 也非常有用。`__func__` 会被替换为当前的 Objective-C 方法名（作为 C 字符串）。`__DATE__` 和 `__TIME__` 会展开为编译执行的日期和时间。

我通常将清单 14-8 中的宏更进一步，定义一组仅在开发期间编译的宏（`DevAssert`、`DevAssertObjectIsClass`、…）、一组在开发和 Beta 测试期间编译的宏（`BetaAssert`、`BetaAssertObjectIsClass`、…）以及一组始终编译的宏（`RelAssert`、`RelAssertObjectIsClass`、…）。这使我能够逐步使用断言，以安全性为代价，生成逐步变小且更快的应用程序版本。

## 异常的替代方案



