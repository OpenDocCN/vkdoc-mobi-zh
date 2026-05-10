# 异常处理

#### 性能

在 Objective-C 中异常使用较少，另一个原因是性能问题。在 Objective-C 中设置和抛出异常的开销要高于 Java。本章后续"异常处理的替代方案"一节将概述除异常外的其他选择。

不过，不要因此就放弃在 Objective-C 中使用异常。我个人就大量使用异常——这很可能源于我在 Java 编程中的经验。我从未见过 Objective-C 的异常处理成为性能瓶颈。我的建议是：使用你最熟悉的编码风格进行编程。如果这种风格包含异常，那就尽管放心使用。只有当性能分析明确显示异常处理是性能问题时，我才会建议考虑其他错误处理方案。

## 未捕获的异常

未捕获的异常是指那些从应用程序代码中逸出，进入运行时框架的异常。异常也可能由系统事件和运行时错误触发而产生。

未捕获异常的处理过程大致可分为四类：

- 在 GUI 应用的主线程中抛出的异常
- 在所有其他情况下抛出的异常
- 由系统事件（如无效内存访问）产生的异常
- 由运行时事件（如向已释放对象发送消息）产生的异常

默认情况下，应用程序主线程中抛出的异常会被 `NSApplication` 框架吸收并忽略。例如，用户选择某个菜单命令触发的操作抛出了未捕获异常，应用程序的主运行循环会丢弃该异常并继续运行。除了可能有一条消息记录到系统控制台外，不会有任何问题提示。你可能曾在某些应用中遇到过这种现象。

在几乎所有其他情况下，未捕获异常会终止线程；如果异常在主线程中抛出，则会终止进程。系统事件和运行时事件会立即终止进程。

你可以使用可选的 `ExceptionHandling` 框架来拦截这些事件。通过异常处理框架，你可以：

- 记录或后处理 GUI 应用主线程中抛出的未捕获异常
- 记录或后处理任何其他情况下抛出的未捕获异常
- 将系统事件转换为未捕获异常，以便记录或后处理
- 将运行时事件转换为未捕获异常，以便记录或后处理
- 使进程挂起而非终止，以便连接调试器或其他开发工具进行事后分析

如需更改未捕获异常的处理方式，请按以下步骤操作：

1. 将你的应用程序链接到 `ExceptionHandling` 框架。
2. `#import <ExceptionHandling/NSExceptionHandler.h>`
3. 使用 `[NSExceptionHandler defaultExceptionHandler]` 获取进程的单例 `NSExceptionHandler` 对象。
4. 要对未捕获异常、系统事件或运行时事件进行特殊处理，可将表 14-1 中的常量通过逻辑"或"运算组合，然后将结果值传递给 `-[NSExceptionHandler setExceptionHandlingMask:]`。参见清单 14-5。
5. 作为第 4 步的替代方案，可以在用户默认属性 `NSExceptionHandlingMask` 中设置异常处理标志。有关用户默认值及其获取方式的更多详情，请参见第 26 章的"用户默认值"一节。
6. 要单独筛选第 4 步或第 5 步中启用的异常处理，请创建一个实现 `-exceptionHandler:shouldHandleException:mask:` 和 `-exceptionHandler:shouldLogException:mask:` 方法的对象，并将其设为 `NSExceptionHandler` 的代理对象。所有异常都会先传递给代理进行检查。代理可以自行处理异常，让 `NSExceptionHandler` 执行默认处理，两者都做，或两者都不做。
7. 出于调试目的要使进程挂起而非终止，可将表 14-2 中的常量通过逻辑"或"运算组合，然后将结果值传递给 `-[NSExceptionHandler setExceptionHangingMask:]`。

设置异常处理行为的最佳位置是应用程序的初始化代码中。这里的"处理"是指 `NSExceptionHandler` 会将异常传递给其代理进行后处理，并选择性地在继续执行前记录异常详情。虽然你的代理对象可以做更多事情，但 `NSExceptionHandler` 本身并不会对异常进行任何实质性的操作。

在 `NSApplication` 中记录或处理顶层异常和底层异常（表 14-1 中的最后四个选项）应仅在开发阶段使用。这些选项会向系统控制台发送大量调试输出，不适用于发布的应用程序。此外，拦截、修改或忽略这些异常可能会改变应用程序框架的正常运行方式。

你的 `NSExceptionHandler` 代理对象只会收到在第 4 步或第 5 步中启用了记录或处理的异常。如果你启用了这些标志，但没有设置代理对象，则仅会记录异常。

> **注意：** `NSExceptionHandler` 会在你的代码执行 `finally` 块之前处理异常。如果在第 4 步或第 5 步中没有设置系统和运行时事件处理，这些事件不会转换为异常。它们会像没有 `NSExceptionHandler` 时一样被正常处理。

**表 14-1.** 异常处理标志

| 常量                                      | 效果                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `NSLogUncaughtExceptionMask`              | 记录未捕获的异常。                                           |
| `NSHandleUncaughtExceptionMask`           | 处理未捕获的异常。                                           |
| `NSLogUncaughtSystemExceptionMask`        | 将系统事件转换为异常并记录。                                 |
| `NSHandleUncaughtSystemExceptionMask`     | 将系统事件转换为异常并处理。                                 |
| `NSLogUncaughtRuntimeErrorMask`           | 将运行时事件转换为异常并记录。                               |
| `NSHandleUncaughtRuntimeErrorMask`        | 将运行时事件转换为异常并处理。                               |
| `NSLogTopLevelExceptionMask`              | 记录应用程序主运行循环中抛出的未捕获异常。                   |
| `NSHandleTopLevelExceptionMask`           | 处理应用程序主运行循环中抛出的未捕获异常。                   |
| `NSLogOtherExceptionMask`                 | 记录应用程序主运行循环中抛出的所有其他异常，包括已捕获的异常。 |
| `NSHandleOtherExceptionMask`              | 处理应用程序主运行循环中抛出的所有其他异常，包括已捕获的异常。 |

你也可以让 `NSExceptionHandler` 对于特定类型的异常"挂起"进程，而不是终止进程。通过将表 14-2 中的常量进行逻辑"或"运算组合，并将结果值传递给 `-[NSExceptionHandler setExceptionHangingMask:]`，即可设置所需的挂起条件。

**表 14-2.** 异常挂起标志

（原文未提供此内容。）



