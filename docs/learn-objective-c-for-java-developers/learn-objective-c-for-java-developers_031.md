# 正式协议（接口）

`正式协议（接口）` 是与类分开定义的。你可能想要了解某个类遵循了哪些协议，或者某个协议声明了哪些方法。表 10-5 中的函数可以识别出某个类遵循的协议，并允许你探查这些协议。如果你只想知道某个对象是否遵循特定协议，请使用前面介绍的 `-[NSObject conformsToProtocol:]` 方法。

**表 10-5. 协议内省函数**

| 函数 | 返回值 |
|----------|---------|
| `objc_getProtocol(const char*)` | 具有该名称的协议 |
| `NSProtocolFromString(NSString*)` | 与 `objc_getProtocol` 相同，但接受一个 Objective-C 字符串对象 |
| `class_copyProtocolList(Class, unsigned int*)` | 该类遵循的协议列表 |
| `protocol_conformsToProtocol(Protocol*, Protocol*)` | 如果该协议遵循另一个协议，则返回 `YES` |
| `protocol_getName(Protocol*)` | 该协议的名称 |
| `protocol_isEqual(Protocol*, Protocol*)` | 如果协议等效，则返回 `YES` |

`class_copyProtocolList` 返回一个以 `NULL` 结尾的 `Protocol` 指针 C 数组。当你使用完毕后，必须使用 `free(void*)` 函数释放这块内存。数组中协议的数量通过传入第二个参数地址的无符号整数返回。如果该参数为 `NULL`，则不返回计数。你可以使用返回的计数或 `NULL` 终止指针来确定列表长度，如代码清单 10-6 所示。

**代码清单 10-6. 列出类的协议**

```
unsigned int protocolCount;
Protocol **protocols = class_copyProtocolList(class, &protocolCount);
NSMutableString *list = [NSMutableString new];
unsigned int i;
for (i = 0; i < protocolCount; i++) {
    Protocol *p = protocols[i];
    if (i != 0)
        [list appendString:@", "];
    [list appendFormat:@"%s", protocol_getName(p)];
}
if (protocolCount != 0)
    NSLog(@"Class %s implements the protocols: %@", class_getName(class), list);
free(protocols);
```

代码清单 10-6 中值得注意的点：`protocol_getName` 和 `class_getName` 返回的名称是 C 字符串，使用 `%s` 格式说明符进行格式化。`list` 变量是一个字符串对象，使用 `%@` 进行格式化。`Protocol` 是一种结构体类型，而不是像 `Class` 和 `Method` 那样的不透明指针类型。因此，引用被声明为指向 `Protocol` 的指针 (`Protocol*`)，而不仅仅是类型 (`Class`, `Method`)。这很令人困惑，不是吗？

## 探查方法

我已经向你展示了如何轻松确定某个对象是否实现了特定方法。使用表 10-6 中的函数，你可以获取某个类实现的所有方法的列表。从 `Method` 值中，你可以获取方法的名称、选择器、实现地址以及参数信息。

**表 10-6. 常用方法内省函数**

| 函数 | 返回值 |
|----------|---------|
| `class_copyMethodList(Class, unsigned int*)` | 以 `NULL` 结尾的 `Method` 数组 |
| `class_getClassMethod(Class, SEL)` | 如果选择器被发送给该类，则返回 `Method` |
| `class_getInstanceMethod(Class, SEL)` | 如果选择器被发送给该类的一个实例，则返回 `Method` |
| `method_getName(Method)` | `Method` 的选择器常量 |
| `sel_getName(SEL)` | 选择器的名称 |
| `NSStringFromSelector(SEL)` | 选择器的名称 |
| `NSSelectorFromString(NSString*)` | 名称对应的选择器 |
| `class_getMethodImplementation(Class, SEL)` | 实现选择器的代码地址 |
| `method_getImplementation(Method)` | 实现 `Method` 的代码地址 |

`class_copyMethodList` 函数的工作方式与 `class_copyProtocolList` 类似。它返回一个以 `NULL` 结尾的 `Method` 引用数组，使用完毕后应使用 `free(void*)` 释放。

`class_getClassMethod` 函数获取特定选择器常量的单个 `Method`。请注意，这些函数仅返回该类自身定义的方法，而不返回其继承的方法。要发现继承的方法，你必须检查其所有超类。

`class_getClassMethod` 和 `class_getInstanceMethod` 这两个函数考虑了向类对象 (`[MyClass message]`) 或类的实例 (`[myObject message]`) 发送消息时的区别。类方法和实例方法是为不同的对象实现的，因此这两个函数的结果会有所不同。

`method_getName` 函数，尽管其名称如此，但返回的是与 `Method` 关联的选择器常量，而不是方法的名称。要将选择器转换为名称，请调用 `sel_getName` 或 `NSStringFromSelector`。要执行相反操作，请调用 `NSSelectorFromString`。

这些函数实际应用的示例可以在代码清单 10-7 中找到。

**代码清单 10-7. 列出类实现的方法**

```
unsigned int methodCount;
Method *methods = class_copyMethodList(class, &methodCount);
NSMutableString *list = [NSMutableString new];
unsigned int i;
for (i = 0; i < methodCount; i++) {
    Method m = methods[i];
    if (i != 0)
        [list appendString:@", "];
    [list appendFormat:@"%s", sel_getName(method_getName(m))];
}
if (methodCount != 0)
    NSLog(@"Class %s implements the methods: %@", class_getName(class), list);
free(methods);
```

`class_getMethodImplementation` 和 `method_getImplementation` 这两个函数执行相同的操作：它们返回实现该方法的代码的执行地址。“发送消息”章节解释了如何直接使用方法的实现地址来调用方法。

有了 `Method` 引用，有一系列 C 函数可用于检查其参数列表、每个参数的类型和大小等。无需深入探讨这些函数，我建议你使用 `-[NSObject methodSignatureForSelector:]` 方法来获取一个 `NSMethodSignature` 对象。

`NSMethodSignature` 实质上表示了 `Method` 结构体所包含的所有信息，但以面向对象的接口呈现。如果你需要坚持使用 C 函数，请参阅 Objective-C 2.0 运行时参考文档。

有了 `NSMethodSignature` 对象，你可以使用 `-getArgumentTypeAtIndex:` 获取每个参数的类型（即其 `@encode()` 常量）。消息 `-methodReturnType` 返回方法返回值的 C 类型。要使用 `NSMethodSignature` 调用方法，请使用它创建一个 `NSInvocation` 对象。这在第 6 章中也有演示。

## 探查属性

在 Objective-C 2.0 中，`@property` 指令将元数据附加到类上，程序员可以在运行时访问这些元数据。这些被称为正式属性。自然地，属性集合将会与实例变量和方法重叠，因为大多数属性都是使用实例变量以及 getter 和 setter 方法实现的。表 10-7 列出了用于检查类的正式属性的常用方法。

**表 10-7. 常用正式属性内省函数**

| 函数 | 返回值 |
|----------|---------|
| `class_copyPropertyList(Class, unsigned int*)` | 以 `NULL` 结尾的 `objc_property_t` 结构体指针数组 |
| `protocol_copyPropertyList(Protocol*, unsigned int*)` | 以 `NULL` 结尾的 `objc_property_t` 结构体指针数组 |
| `class_getProperty(Class, const char*)` | 指定名称属性的 `objc_property_t` 结构体 |
| `protocol_getProperty(Protocol*, const char*, BOOL, BOOL)` | 指定名称属性的 `objc_property_t` 结构体 |
| `property_getName(objc_property_t)` | 属性名称 |
| `property_getAttributes(objc_property_t)` | 描述该属性的属性字符串 |

`…_copyPropertyList` 和 `…_getProperty` 函数获取属性描述列表或单个属性描述。你可以检查类或协议的属性。列表必须使用 `free(void*)` 释放。



函数`property_getName`返回属性的名称。函数`property_getAttributes`返回一个描述该属性的字符串。该描述包含属性类型、属性和名称的编码形式。字符串始终以`T`开头，后跟其类型的`@encode()`常量。诸如`readonly`、`copy`和`retain`之类的属性会分别添加`R`、`C`和`&`字符。其他属性也有相应的表示。类型和属性之后跟随着`V`和属性的正式名称。表 10-8 中列出了一些示例。请注意，`BOOL`值始终编码为有符号字符。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第十章 ■ 内省

***表 10-8。** 示例属性描述*  
属性声明 | 属性字符串
--- | ---
`@property int someInt;` | `Ti,VsomeInt`
`@property id (assign) anyObject;` | `T@,VanyObject`
`@property(readonly) int ceiling;` | `Ti,R,Vceiling`
`@property(getter=isRunning) BOOL running;` | `Tc,GisRunning,Vrunning`
`@property(nonatomic,readonly,copy) id safe;` | `T@,R,C,Vsafe`

### 探索实例变量

用于实例变量的内省函数遵循与用于方法、协议和属性相同的模式。表 10-9 中列出了一些函数，用于获取描述类定义的所有实例变量或仅一个实例变量的`Ivar`结构列表。函数`ivar_getName`、`ivar_getOffset`、`ivar_getTypeEncoding`将揭示每个`Ivar`的名称、在对象结构中的字节偏移量以及类型——但通常不应使用后两个函数来访问变量。要获取或设置实例变量，请调用`object_getIvar(id, Ivar)`或`object_setIvar(id, Ivar, id)`。返回或传递的值是一个对象，该对象会根据需要转换为变量的实际类型（即自动装箱）。

键值编码（Key-Value Coding）是使用这些底层实例变量内省函数实现的。如果您需要以编程方式获取或设置实例变量的值，您可能会发现使用更高级别的`KVC`方法更容易。清单 10-8 演示了如何通过内省将实例变量`name`设置为字符串`@"Hugh"`，这等同于语句`object->name = @"Hugh"`。

**清单 10-8.** 以编程方式设置实例变量
```
Ivar ivar = class_getInstanceVariable([object class],"name");
object_setIvar(object,ivar,@"Hugh");
```

作为`object_getIvar`和`object_setIvar`的替代方案，函数`object_getInstanceVariable(id, const char*, void**)`和`object_setInstanceVariable(id, const char*, void*)`直接获取或设置实例值，而不使用中间对象。两者都作为副作用返回它们识别为目标变量的`Ivar`。getter 函数接受一个指针的地址，该指针将被设置为指向对象中的实际变量，而 setter 函数接受一个指向值的指针，该值将被复制到对象中。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第十章 ■ 内省

***表 10-9。** 常用实例变量内省函数*  
函数 | 返回值
--- | ---
`class_copyIvarList(Class, unsigned int*)` | 以`NULL`结尾的`Ivar`指针数组
`class_getInstanceVariable(Class, const char*)` | 描述命名变量的`Ivar`指针
`ivar_getName(Ivar)` | 变量的名称
`object_getIvar(id, Ivar)` | 对象变量的值（作为对象）
`object_setIvar(id, Ivar, id)` | 无；设置对象变量的值
`object_getInstanceVariable(id, const char*, void**)` | 命名变量的`Ivar`及其值的指针
`object_setInstanceVariable(id, const char*, void*)` | 命名变量的`Ivar`，并将新值复制到其中

### 总结

还有许多其他方法和函数用于操作定义类和对象运行时结构的。本章重点介绍的函数让您可以执行常见形式的内省，通过这些内省，您可以检查对象及其类的几乎每个方面。如果您需要更深入地挖掘（无意双关），我强烈建议您阅读苹果公司的《Objective-C 2.0 运行时参考》[³]。

[³]: Apple, Inc., *Objective-C 2.0 Runtime Reference*, http://developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/, 2008.

[www.it-ebooks.info](http://www.it-ebooks.info/)

