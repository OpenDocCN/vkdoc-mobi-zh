# Foundation 框架：URL 处理与进程间通信

Foundation 框架中的 `NSURLProtocol` 和 `NSURLProtocolClient` 类支持创建用于从 URL 加载数据的自定义协议。`NSURLProtocol` 是一个抽象类，提供了执行特定协议 URL 加载的基本结构。它包含了用于创建 `NSURLProtocol` 对象、注册/注销协议类、请求管理、获取协议属性以及启动/停止下载的方法。`NSURLProtocolClient` 是一个由 `NSURLProtocol` 子类使用，用于与 URL 加载系统通信的协议。

## 进程间通信

Foundation 框架包含一组支持进程间通信的类。具体来说，它们提供了创建和使用通信通道的功能。

### 通过管道通信

`NSPipe` 类封装了一个*管道*，这是一种用于进程间通信的单向通道。其 API 包括创建和初始化管道的方法，以及获取管道对应 `NSFileHandle` 实例的方法。`NSTask` 类提供了几个方法，用于设置进程的输入和输出通道（`setStandardOutput:`、`setStandardInput:`）。清单 11-18 演示了如何使用 `NSTask` 和 `NSPipe` API 创建并启动一个任务（Unix 命令 `/bin/ls` 仅列出当前目录中的文件）、设置任务的标准输出，然后在任务完成后检索并记录写入管道的标准输出。

*清单 11-18.* 使用 `NSPipe` 和 `NSTask` 调用进程

```
NSTask *task = [[NSTask alloc] init];
[task setLaunchPath:@"/bin/ls"];
NSPipe *outPipe = [NSPipe pipe];
[task setStandardOutput:outPipe];
[task launch];
NSData *output = [[outPipe fileHandleForReading] readDataToEndOfFile];
NSString *lsout = [[NSString alloc] initWithData:output
                                        encoding:NSUTF8StringEncoding];
NSLog(@"/bin/ls output:\n%@", lsout);
```

### 通过端口通信

`NSPort`、`NSMachPort`、`NSMessagePort` 和 `NSSocketPort` 提供了用于线程或进程间通信的底层机制，通常通过 `NSPortMessage` 对象实现。

`NSPort` 是一个抽象类。它包含创建和初始化端口、创建到端口的连接、设置端口信息以及监视端口的方法。`NSMachPort`、`NSMessagePort` 和 `NSSocketPort` 是 `NSPort` 的具体子类，用于特定类型的通信端口。`NSMachPort` 和 `NSMessagePort` 仅允许本地（同一台机器上）通信。此外，`NSMachPort` 用于 Mach 端口，这是 OS X 中的基本通信端口。`NSSocketPort` 既允许本地通信也允许远程通信，但在本地通信时效率可能低于其他端口。在创建 `NSConnection` 实例时，可以将端口实例作为参数提供（使用 `initWithReceivePort:sendPort:` 方法）。您还可以通过 `NSPort` 的 `scheduleInRunLoop:forMode:` 方法将端口添加到运行循环中。

由于端口是一种非常底层的进程间通信机制，您应尽可能使用分布式对象来实现应用程序间通信，仅在必要时才使用 `NSPort` 对象。此外，当您使用完端口对象后，必须通过 `NSPort` 的 `invalidate` 方法显式地使该对象失效。

`NSSocketPort` 和 `NSPortMessage` 类仅可在 OS X 平台上使用。

### 端口注册

`NSPortNameServer`、`NSMachBootstrapServer`、`NSMessagePortNameServer` 和 `NSSocketPortNameServer` 提供了端口注册服务的接口，用于检索 `NSMachPort`、`NSMessagePort` 和 `NSSocketPort` 的实例。

`NSPortNameServer` 为分布式对象系统使用的端口注册服务提供了一个面向对象的接口。`NSConnection` 对象使用它来相互联系并在网络上分发对象。您很少需要直接与 `NSPortNameServer` 实例交互。`NSMachBootstrapServer`、`NSMessagePortNameServer` 和 `NSSocketPortNameServer` 是 `NSPortNameServer` 的子类，它们返回相应的端口实例（`NSMachPort`、`NSMessagePort`、`NSSocketPort`）。

`NSPortNameServer`、`NSMachBootstrapServer`、`NSMessagePortNameServer` 和 `NSSocketPortNameServer` 类仅可在 OS X 平台上使用。

### 小结

在本章中，您开始了对 Foundation 框架的回顾，重点介绍了为大多数 Objective-C 程序提供通用、常用功能的类。现在，您应该熟悉提供以下功能的 Foundation 框架类：

- 网络服务
- 应用程序服务
- 文件系统工具
- URL 处理
- 进程间通信
- 并发与线程

