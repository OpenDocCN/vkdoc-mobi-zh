# 错误处理

一些 Objective-C API 和许多程序员遵循传统的 C 语言错误处理模式：测试函数或方法的返回值以确定成功或失败。清单 14-9 展示了一些典型示例。这里所采用的编程理念是，异常应保留用于运行时错误（如索引越界、无效对象、缺少应用程序资源、内存不足）以及其他应在开发过程中从应用程序中消除的编程错误。可预见的、合理预期可能发生的失败（如文件未找到、数据库为空、名称重复）应使用错误代码或错误对象来处理。本节描述了不使用异常处理错误的最常用技术，并稍后解释如何将两者结合。

## 清单 14-9. 错误处理示例

```objectivec
// Simple Error
NSString *imagePath = [[NSBundle mainBundle] pathForImageResource:@"picture.png"];
if (imagePath==nil) {
    NSLog(@"missing image resource");
    return;
}

// POSIX Error
int fd = open("filename",O_RDONLY);
if (fd<0) {
    NSLog(@"open() failed with error %d",errno);
    return;
}

// Core Foundation Error
QTUUID quickTimeUUID;
OSErr err = QTCreateUUID(&quickTimeUUID,0);
if (err!=noErr) {
    NSLog(@"could not create UUID, error %d",err);
    return;
}

// Cocoa Error
NSError *error = nil;
NSDictionary *attributes;
attributes = [[NSFileManager defaultManager] attributesOfItemAtPath:@"filename"
                                                             error:&error];
if (error!=nil) {
    NSLog(@"could not get attributes of file: %@",[error localizedDescription]);
    [self presentError:error];
    return;
}
```

#### 简单错误

当任何函数或方法未能实现其目标并返回空值时，就会发生简单错误。清单 14-9 中的 `-pathForImageResource:` 消息是一个很好的例子。该方法返回所需图像文件的路径，如果找不到此类文件，则返回 `nil`。你的代码应该测试结果——除非你设计代码使用了第 7 章描述的缺席行为——并提供默认值、返回 `NSError`、抛出异常或引发断言。

## POSIX 错误代码

大多数 BSD 和 POSIX 函数返回一个值来指示函数的成功或失败。在清单 14-9 中，`open(...)` 函数如果成功则返回一个文件描述符整数，如果失败则返回一个负值。描述失败原因的代码从线程局部变量 `errno` 中读取。注意，`errno` 实际上不是一个变量，而是一个预处理器宏，它展开为一个函数调用以获取错误代码。该变量仅保证在下一个 POSIX 函数被调用之前包含错误代码，因此在失败后应立即获取代码并保存。

## Core Foundation 错误代码

许多 Core Foundation 函数直接向调用者返回结果代码。结果代码的类型为 `OSError` 或 `OSStatus`，通常是负数。函数的成功由值 `noErr` 指示，如清单 14-9 所示。由于此类函数返回错误代码，因此使用指向变量的指针来返回任何附加值。

#### Cocoa 错误

现代 Cocoa 类和函数在操作未能成功完成时倾向于返回 `NSError` 对象。消息的发送者总是包含一个指向 `nil` `NSError` 指针变量的指针，如清单 14-9 所示。如果操作失败，该方法会创建一个新的 `NSError` 对象，并将其地址存储在发送者的变量中。发送者可以检查其变量以查看接收者是否返回了 `NSError`。当失败细节将呈现给用户时，鼓励使用 `NSError`。

`NSError` 类包含许多设计特性，使其成为设计良好且灵活的错误处理系统中不可或缺的一部分。将 `NSError` 对象集成到你的应用程序中并非易事，但如果你的目标是提供一致、可本地化、模块化和灵活的错误显示与恢复，则强烈推荐这样做。

本书篇幅有限，无法深入 `NSError` 对象的所有细节，但以下各节将提供足够的概述，让你能够理解其实用性。在应用程序中设计 `NSError` 管理之前，请仔细阅读《Cocoa 错误处理编程指南》。

##### 错误域

每个 `NSError` 对象都有一个域。该域影响着其属性（特别是数字错误代码）的解释方式。例如，POSIX 和 Foundation 函数都返回一个整型错误值来描述失败。域决定了应使用哪一组常量来解释该值。主要的域列于表 14-5 中。鼓励你为应用程序特定的错误定义自己的域。

**表 14-5. Cocoa 错误域**

| 域 | 描述 |
| --- | --- |
| `NSMachErrorDomain` | 内核返回的错误代码。 |
| `NSPOSIXErrorDomain` | POSIX 和 BSD 函数返回的错误代码。 |
| `NSOSStatusErrorDomain` | Core Foundation 函数返回的错误代码。 |
| `WebKitErrorDomain` | WebKit 生成的错误。 |
| `NSURLErrorDomain` | 与 URL 相关的错误。 |
| `NSXMLParserErrorDomain` | XML 解析器错误。 |
| `NSCocoaErrorDomain` | 所有不属于以上特定域的 Cocoa 框架错误。 |

##### 自定义与显示

`NSError` 对象的主要作用之一不仅仅是处理错误（异常可以很好地做到这一点），而是向用户显示错误并根据用户的输入采取行动。该过程的前两步通过响应者链执行。响应者链是视觉层次中通向当前活动界面元素的组件链。它在第 20 章中有完整描述。

要将错误显示给用户，请将 `NSError` 传递给响应者链中的任何对象——最好是叶组件——通过向其发送 `-presentError:` 消息。响应者将依次通过 `-willPresentError:` 消息将 `NSError` 传递给响应者链中的每个对象。这给了你的应用程序每一层一个机会来拦截错误并将其替换为更具体的内容。例如，一个“添加配乐”按钮可以将一个通用的“文件未找到” `NSError` 替换为“配乐不可用。您想选择另一首歌曲还是使用默认配乐？”的错误对象。在所有响应者对象处理完错误后，最终的错误会以模态对话框的形式呈现给用户。如果错误发生在窗口上下文中，推荐的消息是 `-presentError:modalForWindow:delegate:didPresentSelector:contextInfo:`，它会将错误以模态表单的形式附加到窗口上。

##### 本地化

`NSError` 类提供了一种本地化错误消息的机制。本地化的消息可以存储在 `NSError` 的 `userInfo` 字典中，使用预定义的键，也可以通过重写其方法来动态生成。使用哪种方法取决于你的需求。表 14-6 列出了将显示给用户的四种消息以及返回它们的方法。每个属性访问器的默认实现是从错误的 `userInfo` 字典中检索第三列的值。

**表 14-6. `NSError` 本地化方法和 `userInfo` 键**

| 对话框文本 | `NSError` 属性 | `NSError` `userInfo` 键 |
| --- | --- | --- |
| 错误描述 | `localizedDescription` | `NSLocalizedDescriptionKey` |
| 失败原因 | `localizedFailureReason` | `NSLocalizedFailureReasonErrorKey` |



