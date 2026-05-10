# 分布式通知与分布式对象

`分布式通知`和`分布式对象`是两种主要的进程间高级通信技术。它们都是面向对象的设施，能够自动归档或序列化对象数据，以便传输到另一个进程或系统。`端口`、`管道`或`socket`被用于传输这些序列化数据。

本节将简要介绍 Objective-C 中与端口、管道和 socket 相关的接口，然后简要提及分布式通知，最后深入探讨分布式对象。分布式对象是面向对象进程间通信的巅峰，本章剩余大部分内容都将围绕它展开。

#### 底层通信

Objective-C 提供了四个关键类来表示串行数据源：

- `NSPort`
- `NSPipe`
- `NSStream`
- `NSFileHandle`

这些类在功能和特性上有大量重叠之处。

例如，可以通过`NSPort`、`NSPipe`和`NSFileHandle`连接到 BSD socket。它们都允许通过数据通道发送和接收串行数据。具体选择哪个类，很大程度上取决于使用场景。需要由运行循环处理的数据源必须是`NSPort`的子类。用于连接进程的 POSIX 管道（更常见的叫法是标准输入、标准输出和标准错误）可以是`NSPipe`或`NSFileHandle`对象。网络服务则提供`NSStream`对象与已连接的进程进行通信。

## NSPort

`NSPort`是将数据源连接到运行循环的基类。推送到端口上的消息由运行循环处理。`NSMachPort`是一个子类，用于连接到 Mach 内核端口，实现直接的进程间通信。`NSSocketPort`可以连接到 BSD 管道或 socket，为不同进程和系统之间提供等效的功能。`NSPort`是分布式对象的基础，本章后面的"分布式对象"部分将演示其中的几种类型。

`NSMachPort`效率极高，是运行循环最常用的端口类型。用户事件、系统事件、延迟消息、定时事件以及许多其他底层消息，都通过应用程序的运行循环进行处理。大多数事件由系统或应用程序内部推送到运行循环的 Mach 端口上。Mach 端口也可用于在进程之间发送消息。其唯一限制在于操作系统的安全模型。所有 Mach 内核端口都存在于一个称为引导命名空间的域中。每个登录到 Mac OS X 系统的用户都会创建一个引导命名空间。一个进程只能与其命名空间及其父命名空间中的 Mach 端口进行通信。因此，应用程序可以使用 Mach 端口与同一用户启动的另一个进程或系统守护进程建立通信，但不能与另一个用户启动的进程通信。

当运行循环需要与其引导命名空间之外的进程或另一台计算机系统通信时，会使用`NSSocketPort`。`NSSocketPort`可以连接到多种不同的数据源；其中最有用的两种是 BSD 管道和 socket。管道可以是文件系统中的命名管道。文件系统名称对所有进程都是公开的，为不同引导命名空间中的两个进程提供了通信手段。网络 socket 允许两个进程通过网络传输协议交换数据。其优势在于，另一个进程可以运行在同一台机器上，也可以运行在数千英里之外。其缺点是网络端口通常可以从计算机系统外部访问，这可能不适合某些通信场景，并存在安全隐患。

## NSPipe

`NSPipe`本质上是对一对`NSFileHandle`对象的轻量封装。`NSPipe`用于与 BSD 管道交互。通常，这些管道就是连接进程的传统标准输入、标准输出和标准错误管道。清单 13-1 演示了如何启动一个可执行文件并捕获新进程的文本输出。

### 清单 13-1. 捕获标准输出

**Java**

```java
ProcessBuilder pb = new ProcessBuilder("/bin/echo","Hello, Objective-C");
try {
    Process echo = pb.start();
    InputStream stdOut = echo.getInputStream();
    int c = (int)' ';
    System.out.print("echo says:");
    do {
        System.out.print((char)c);
        c = stdOut.read();
    } while (c!=(-1));
} catch (IOException e) {
    e.printStackTrace();
}
```

**Objective-C**

```objc
NSTask *echo = [NSTask new];
NSPipe *stdOut = [NSPipe pipe];
[echo setLaunchPath:@"/bin/echo"];
[echo setArguments:[NSArray arrayWithObject:@"Hello, Objective-C"]];
[echo setStandardOutput:stdOut];
[echo launch];

NSFileHandle *outStream = [stdOut fileHandleForReading];
NSData *output = [outStream readDataToEndOfFile];
NSLog(@"echo says: %@",[NSString stringWithCString:[output bytes]
                                            length:[output length]]);
```

在 Java 中，`java.lang.Process`对象会创建所需的`java.io.InputStream`或`java.io.OutputStream`对象并直接提供给你。而在 Objective-C 中，一切恰好相反。你需要先创建用于通信的`NSPipe`或`NSFileHandle`对象，然后通过`-setStandardInput:`、`-setStandardOutput:`或`-setStandardError:`消息将其传入。这必须在进程启动之前完成。启动时，`NSTask`会将你提供的管道或文件句柄连接到进程的实际管道上。另外请注意，术语的含义是相反的。在 Objective-C 中，`standardOut`属性是指进程的标准输出（即进程的输出）。在 Java 中，`java.lang.Process.getInputStream`获取的是连接到进程标准输出的`InputStream`。换句话说，Objective-C 中的管道标识是基于进程的视角。而在 Java 中，则是基于父进程的视角。

要获取实际的输入数据，需要从管道中获取用于读取的`NSFileHandle`。也可以完全绕过`NSPipe`，因为`-setStandardInput:`、`-setStandardOutput:`和`-setStandardError:`消息也接受`NSFileHandle`对象。因此，清单 13-1 中的代码可以轻松改写为直接设置一个`NSFileHandle`作为其标准输出的连接，效果是相同的。

## NSFileHandle

`NSFileHandle`是对 POSIX 文件进行通用封装的类。当与管道和 socket 一起使用时，它们就变成了流式接口。`NSFileHandle`的方法已在"文件"章节中描述过。当与管道文件一起使用时，那些对串行数据没有意义的方法——特别是`-offsetInFile`、`-seekToFileOffset:`和`-seekToEndOfFile`——不应被使用。单向输出管道不应发送任何`-read...`消息，而单向输入管道则不会接受`-writeData:`消息。

与 Java 的`InputStream`类似，逻辑上的文件结束（EOF）条件仅在管道的输入侧关闭时才存在。因此，清单 13-1 中的`-[NSFileHandle readDataToEndOfFile]`方法会一直挂起，直到管道的另一端关闭，即使管道中的所有数据都已被读取完毕。

`NSFileHandle`的`-readInBackgroundAndNotify`、`-readToEndOfFileInBackgroundAndNotify`和`-waitForDataInBackgroundAndNotify`方法在与管道和 socket 一起使用时特别有用。这些消息会创建一个新线程，该线程等待管道中出现数据，然后发布一个你的应用程序可以观察到的通知。`-waitForData…`仅仅通知你数据已可用，但不会读取任何数据。而`-readInBackground…`



