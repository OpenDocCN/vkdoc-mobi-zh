# 5. 对象

**摘要**

Objective-C 对象是同时包含行为和属性的实体。行为通过方法编码，而属性通过属性编码。对象还可以包含私有实例变量。当需要存储数据但又无需共享时，会使用私有实例变量。

## 对象定义

Objective-C 对象是同时包含行为和属性的实体。行为通过方法编码，而属性通过属性编码。对象还可以包含私有实例变量。当需要存储数据但又无需共享时，会使用私有实例变量。

### NSObject 类

`NSObject` 是 Objective-C 中的根类。类是一种定义，它包含了使对象的方法和属性正常工作所需的所有代码。`NSObject` 被称为根类，因为它包含了使对象在 Objective-C 中工作所需的所有代码，并且所有其他类都继承自 `NSObject` 类。



### 对象声明

类被用作一种数据类型。数据类型用于声明变量，而每种数据类型都可以有多个变量。类则用于声明对象，一个类可以拥有多个对象。

以下是声明一个 `NSObject` 对象的方法：

`NSObject object;`

### 对象构造函数

虽然数据类型的变量可以直接赋值，但对象需要借助名为构造函数的函数。构造函数为对象分配内存资源，并执行对象正常运行所需的任何初始化设置。通常情况下，你会看到构造函数被拆分为两个函数：`alloc` 和 `init`。

`object = [[NSObject alloc] init];`

`init` 函数的名称有时会不同，但它通常以字母 `init` 开头。例如，这是一个指向我网站的 `NSURL` 对象的构造函数：

`NSURL *url = [[NSURL alloc] initWithString:@"https://mobileappmastery.com"];`

请注意，这里使用的是 `initWithString:` 而不是 `init`。除了遵循惯例之外，构造函数的命名并没有强制性的规则。

尽管 `alloc` 和 `init` 的组合模式最为常见，但你也会看到使用其他函数名称或 `new` 关键字来创建对象。

`NSDate *today = [NSDate date];`

`NSObject *object2 = [NSObject new];`

虽然 `new` 构造函数不常见，但 `new` 关键字可以用来替代 `alloc` 和 `init`。除了 `new`、`alloc` 和 `init` 之外的其他构造函数，则用于创建临时对象。上面的 `date` 对象就是一个临时使用的示例，因为你通常只是想获取一个时间戳后继续执行其他操作，没有理由长时间维护这样一个对象。

**注意**  
像示例中的 `date` 对象这样的临时对象，在未使用 ARC 进行内存管理的项目中更为常见。ARC（自动引用计数）是一种管理每个对象内存需求的系统。使用 ARC 构建的项目，在需要特定功能时会使用诸如上述 `date` 对象之类的临时对象，但无需长时间维护该对象。

### 对象格式说明符

当你使用 `NSLog` 打印输出数据类型值时，必须使用格式说明符，例如 `%lu`、`%li`、`%f` 或 `%i`。该值会被替换到 `NSLog` 字符串中，让你能够观察变量的值。对象也可以这样做。

`NSObject` 对象以及所有继承自 `NSObject` 的对象都使用 `%@` 格式说明符。从 `NSLog` 获得的输出取决于对象的类型。如果你像这样打印输出上文示例中的对象：

`NSLog(@"object = %@", object);`

你将获得包含对象详细信息（包括类名和内存地址）的输出。

`object = <NSObject: 0x10010a0c0>`

其他对象会返回更具体的信息；返回的内容取决于对象的类型。如果你对 `url` 这个 `NSURL` 对象尝试同样的操作：

`NSLog(@"website = %@", url);`

控制台将显示网站的 URL。

`website = https://mobileappmastery.com`

### 消息

当你希望对象执行某个操作时，可以向该对象发送一条消息。发送消息会指示对象执行其类中与消息相对应的方法。

例如，你可以通过向 `NSFileManager` 对象发送一条消息来删除共享目录中的文件。

`NSFileManager *fileManager = [NSFileManager defaultManager];`

`[fileManager removeItemAtPath:@"/Users/Shared/studyreport.txt" error:nil];`

上面第一行代码声明了一个名为 `fileManager` 的 `NSFileManager` 对象。在第二行代码中，你可以看到发送消息的示例。消息是 `removeItemAtPath:error:`，你通过写出这个消息并包含参数（此处为要删除的项目和一个可选的错误对象）来发送它。所有这些内容都包含在方括号 `[ ]` 内，并以分号结尾。

如果你查看 `NSFileManager` 头文件中的类定义，会发现此方法的声明：

```
- (BOOL)removeItemAtPath:(NSString *)path error:(NSError **)error;
```

这个方法返回一个 `BOOL` 值，但此处未使用该返回值。

