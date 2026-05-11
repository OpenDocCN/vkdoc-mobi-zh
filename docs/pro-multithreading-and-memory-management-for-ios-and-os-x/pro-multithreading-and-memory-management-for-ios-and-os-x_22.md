# ARC、Block 与 GCD 示例

让我们来看一个结合使用 ARC、Block 和 GCD 的示例。以下源代码从 URL 读取数据并在主线程上显示结果。这是 iOS 应用中非常典型的用例，你可以将其作为应用程序的一部分直接使用。它展示了 ARC、Block 和 GCD 如何用于开发诸如 Twitter 或 Tumblr 客户端之类的应用。

源代码随附了注释进行说明。

```objectivec
NSString *url = @"http://images.apple.com/"
    "jp/iphone/features/includes/camera-gallery/03-20100607.jpg"

/*
 * 在主线程上，从指定的 URL 开始异步下载数据。
 */

[ASyncURLConnection request:url completeBlock:^(NSData *data) {

    dispatch_queue_t queue =
        dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_async(queue, ^{

         /*
          * 在全局派发队列上处理已下载的数据。
          * 这不会阻塞主线程，因此该任务可以执行较长时间。
          */

        dispatch_async(dispatch_get_main_queue(), ^{

             /*
              * 这里在主派发队列上使用处理结果。
              * 在用户界面上显示结果。
              */
        });
    });

} errorBlock:^(NSError *error) {

     /*
      * 发生错误时
      */

    NSLog(@"error %@", error);
}];
```

处理已下载的数据在其他线程上执行，以避免阻塞主线程。`dispatch_get_global_queue` 函数用于获取一个默认优先级的全局派发队列，`dispatch_async` 函数则在全局派发队列上处理数据。当处理完成后，必须在主线程上更新用户界面以显示结果。这通过 `dispatch_get_main_queue` 函数获取主派发队列，再使用 `dispatch_async` 函数执行任务来实现。

顺便提一下，下载操作本身是否应该使用 GCD 实现，以使其在其他线程上运行从而避免阻塞主线程？答案是否定的。对于网络编程，强烈建议使用异步 API。请参阅以下 WWDC 2010 的视频讲座。

-   WWDC 2010 Session 207 — 针对 iPhone OS 的网络应用，第一部分
-   WWDC 2010 Session 208 — 针对 iPhone OS 的网络应用，第二部分

这些是关于网络编程的讲座。其中明确提到，对于网络编程，“线程是有害的™”。当使用线程进行网络编程时，容易产生过多线程。例如，如果为每个连接创建一个线程，每个线程的栈会瞬间消耗大量内存。Cocoa 框架中已有现成的异步网络 API。请务必使用异步网络 API。**切勿**为网络操作使用线程。

源代码中的 `ASyncURLConnection` 类是一个用于网络编程的类，其基类为 Foundation 框架中的 `NSURLConnection`，该类用于通过网络异步加载数据。现在让我们来看看这个类是如何实现的。

## `ASyncURLConnection.h`

```objectivec
#import <Foundation/Foundation.h>

/*
 * 通过为 Block 类型变量使用 typedef，
 * 源代码将具备更好的可读性。
 */

typedef void (^completeBlock_t)(NSData *data);
typedef void (^errorBlock_t)(NSError *error);

@interface ASyncURLConnection : NSURLConnection
{
     /*
      * 由于启用了 ARC，以下所有变量
      * 在未显式指定修饰符时，均使用 __strong 进行修饰。
      */

    NSMutableData *data_;
    completeBlock_t completeBlock_;
    errorBlock_t errorBlock_;
}

/*
 * 为提高源代码可读性，
 * 参数中使用了经过 typedef 定义的 Block 类型变量。
 */

+ (id)request:(NSString *)requestUrl
    completeBlock:(completeBlock_t)completeBlock
    errorBlock:(errorBlock_t)errorBlock;

- (id)initWithRequest:(NSString *)requestUrl
    completeBlock:(completeBlock_t)completeBlock
    errorBlock:(errorBlock_t)errorBlock;
@end
```

## `ASyncURLConnection.m`

```objectivec
#import "ASyncURLConnection.h"

@implementation ASyncURLConnection

+ (id)request:(NSString *)requestUrl
    completeBlock:(completeBlock_t)completeBlock
    errorBlock:(errorBlock_t)errorBlock
{
     /*
      * 如果禁用了 ARC，
      * 此方法应在调用 autorelease 后返回对象：
      *          * id obj = [[[self alloc] initWithRequest:requestUrl
      *   completeBlock:completeBlock errorBlock:errorBlock];
      * return [obj autorelease];
      *
      * 由于此方法名不以 alloc/new/copy/mutableCopy 开头，
      * 返回的对象已自动注册到 autoreleasepool 中。
      */

    return [[self alloc] initWithRequest:requestUrl
        completeBlock:completeBlock errorBlock:errorBlock];
}

- (id)initWithRequest:(NSString *)requestUrl
    completeBlock:(completeBlock_t)completeBlock
    errorBlock:(errorBlock_t)errorBlock
{
    NSURL *url = [NSURL URLWithString:requestUrl];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];

    if ((self = [super initWithRequest:request
            delegate:self startImmediately:NO])) {
        data_ = [[NSMutableData alloc] init];

         /*
          * 为确保安全使用传入的 Block，
          * 调用实例方法 'copy' 将 Block 复制到堆上。
          */
        completeBlock_ = [completeBlock copy];
        errorBlock_ = [errorBlock copy];

        [self start];
    }

     /*
      * 具有 __strong 修饰符的成员变量
      * 拥有所创建的 NSMutableData 类对象的所有权。
      *
      * 当该对象被废弃时，带有 __strong 修饰符的成员变量的强引用消失。
      * NSMutableData 类对象和 Block 将被自动释放。
      *
      * 因此，无需显式实现 dealloc 实例方法。
      */

    return self;
}

- (void)connection:(NSURLConnection *)connection
    didReceiveResponse:(NSURLResponse *)response
{
    [data_ setLength:0];
}

- (void)connection:(NSURLConnection *)connection
    didReceiveData:(NSData *)data
{
    [data_ appendData:data];
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
     /*
      * 执行分配给下载成功回调的 Block。
      * 传统的委托回调可以替换为 Block。
      */

    completeBlock_(data_);
}

- (void)connection:(NSURLConnection *)connection
    didFailWithError:(NSError *)error
{
     /*
      * 执行分配给错误的 Block。
      */

    errorBlock_(error);
}

@end
```

当下载完成或发生错误时，`NSURLConnection` 类会调用其委托对象上的方法。`ASyncURLConnection` 类继承自 `NSURLConnection` 类，并且可以将 Block 设置为下载完成和错误发生时的回调。使用这个类，应用程序的源代码可以更简洁。ARC 能够妥善管理用于存储已下载数据的 `NSMutableData` 类对象以及用于回调的 Block。由于成员变量使用 `__strong` 修饰，你无需显式调用 `retain` 或 `release` 方法。实际上，在启用 ARC 的环境中，你也不能调用这些方法。而且，你也不需要实现 `dealloc` 实例方法。

即使在这段简单的源代码中，你也必须认识到 ARC、Block 和 Grand Central Dispatch 的强大之处。

## 附录 B



## 过渡至 ARC 版本说明

- [`http://developer.apple.com/library/mac/#releasenotes/ObjectiveC/RN-TransitioningToARC/_index.html`](http://developer.apple.com/library/mac/#releasenotes/ObjectiveC/RN-TransitioningToARC/_index.html)

## Apple 官方 ARC 编程指南

如果想知道关于 ARC 的知识，应当首先阅读本文档。如果你在阅读本文档之前正在看这本书，那也没关系 :-)，但稍后还是建议你读一下。

## LLVM 文档——自动引用计数

- [`http://clang.llvm.org/docs/AutomaticReferenceCounting.html`](http://clang.llvm.org/docs/AutomaticReferenceCounting.html)

这份文档类似于 ARC 的规范文档。当你需要确认其规范细节时，请查阅此文档。

## 高级内存管理编程指南

- [`http://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html`](http://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html)

这是一份由 Apple 撰写的文档，详细解释了引用计数方式的内存管理。

## 入门指南：构建并运行 Clang

- [`http://clang.llvm.org/get_started.html`](http://clang.llvm.org/get_started.html)
- Subversion 仓库 [`http://llvm.org/svn/llvm-project/cfe/trunk`](http://llvm.org/svn/llvm-project/cfe/trunk)

本文解释了如何获取支持 ARC 的编译器 clang（LLVM 编译器 3.0）的源代码。其中也包含了该源代码的仓库。例如，仓库中的以下源代码包含 ARC 的实现代码：

- `llvm/tools/clang/lib/CodeGen/CGObjC.cpp`
- `llvm/tools/clang/lib/CodeGen/CGObjCMac.cpp`

## objc4 版本 493.9

- [`http://www.opensource.apple.com/source/objc4/objc4-493.9/`](http://www.opensource.apple.com/source/objc4/objc4-493.9/)

这是 Apple 的 Objective-C 运行时库的一个实现，源代码是开放的。上述文档“LLVM 文档——自动引用计数”中解释的 Objective-C 运行时 API 正是在这个库中实现的。

与 ARC 相关的 API 多数位于以下文件中：

- `runtime/objc-arr.mm`

与 `__weak` 限定符相关的 API 位于：

- `runtime/objc-weak.mm`

## GNUstep libobjc2 版本 1.5

- [`http://gnustep.blogspot.com/2011/07/gnustep-objective-c-runtime-15-released.html`](http://gnustep.blogspot.com/2011/07/gnustep-objective-c-runtime-15-released.html)
- [`http://thread.gmane.org/gmane.comp.lib.gnustep.general/36358`](http://thread.gmane.org/gmane.comp.lib.gnustep.general/36358)
- [`http://svn.gna.org/viewcvs/gnustep/libs/libobjc2/1.5/`](http://svn.gna.org/viewcvs/gnustep/libs/libobjc2/1.5/)

`libobjc2` 是 GNUstep 项目提供的支持 ARC 的 Objective-C 运行时库实现。它相当于 Apple 的 Objective-C 运行时库 `objc4`。

## Apple 对 C 语言的扩展

- [`http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1370.pdf`](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1370.pdf)

这是一份关于 Apple 实现的 C 语言扩展的概览文档。从中我们可以看出，ARC 的基本思想，即 `__strong` 限定符和 `__weak` 限定符，在实现 Blocks 期间就已经被实现了。

## Blocks 提案，N1451

- [`http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1451.pdf`](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1451.pdf)

这是针对 C 语言规范“ISO/IEC 9899:1999(E) Programming Language-C (Second Edition)”（即 C99）中 Blocks 的一份提案。它由 Apple 提交给 C 语言国际标准化工作组 ISO/IEC JTC1/SC22/WG14（负责 C 语言标准化的组织）。目前看来，似乎还没有将 Blocks 添加到 C 标准中的相关行动。

## 关于 Blocks 的演示文稿

- [`http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1457.pdf`](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1457.pdf)

这是 Apple 关于 Blocks 的一份演示文稿。它似乎被用于向 WG14 工作组推广 Blocks。这份文稿有助于概览性地了解 Blocks。

## Blocks 语言规范

- [`http://clang.llvm.org/docs/BlockLanguageSpec.txt`](http://clang.llvm.org/docs/BlockLanguageSpec.txt)

这是 LLVM 中包含的 Blocks 语言规范。通过结合下方的“Blocks 实现规范”一同阅读，你可以学习如何实现一个支持 Blocks 的编译器和运行时库。如果你想让一个编译器支持 Blocks，则必须阅读本文档。

## Blocks 实现规范

- [`http://clang.llvm.org/docs/Block-ABI-Apple.txt`](http://clang.llvm.org/docs/Block-ABI-Apple.txt)

这是 LLVM 中包含的 Blocks 实现规范。当你拥有支持 Blocks 的编译器，但缺少运行时库时，通过阅读本文档可以找到解决方案。

## libclosure 版本 53

- [`http://www.opensource.apple.com/source/libclosure/libclosure-53/`](http://www.opensource.apple.com/source/libclosure/libclosure-53/)

`libclosure` 是 Apple 提供的 Blocks 运行时库。该库提供了诸如 `Block_copy` 和 `Block_release` 函数等用于 Blocks 的 API。

## plblocks

- [`http://code.google.com/p/plblocks/`](http://code.google.com/p/plblocks/)

这是一个运行时库，用于在不支持 Blocks 的旧版操作系统（如 OS X 10.5 Snow Leopard 和 iOS 3.0）上支持 Blocks。在这些旧版操作系统中，运行时库没有提供诸如 `Block_copy` 或 `Block_release` 这样与 Block 相关的函数。通过使用 `plblocks` 自身实现的这些功能，Blocks 即可在旧版操作系统上正常工作。

## libdispatch 版本 187.5

- [`http://www.opensource.apple.com/source/libdispatch/libdispatch-187.5/`](http://www.opensource.apple.com/source/libdispatch/libdispatch-187.5/)

`libdispatch` 是 Apple 提供的一个库，用于提供诸如调度队列 (dispatch queues) 等 GCD API。

## Libc 版本 763.11

- [`http://www.opensource.apple.com/source/Libc/Libc-763.11/`](http://www.opensource.apple.com/source/Libc/Libc-763.11/)

其中实现了 `pthread_workqueue` API。该 API 源自于 GCD 的 `pthread_workqueue`。

相关 API 的源代码位于：

- `pthreads/pthread.c`
- `pthreads/pthread_workqueue.h`

## xnu 版本 1699.22.81

- [`http://www.opensource.apple.com/source/xnu/xnu-1699.22.81/`](http://www.opensource.apple.com/source/xnu/xnu-1699.22.81/)

这是 XNU 内核的源代码，XNU 是 Mac OS X 和 iOS 的核心。你可以在以下源代码中看到 XNU 内核的工作队列 (workqueue) 实现：

- `bsd/kern/pthread_synch.c`
- `osfmk/kern/thread.c`

## libdispatch 项目页面

- [`http://libdispatch.macosforge.org/`](http://libdispatch.macosforge.org/)

这是 Apple 的 `libdispatch` 开源项目页面。你可以阅读 `libdispatch` 的邮件列表、将 `libdispatch` 移植到其他操作系统的相关信息等。

## libdispatch 移植项目

- [`https://www.heily.com/trac/libdispatch`](https://www.heily.com/trac/libdispatch)

这是 `libdispatch` 的移植项目页面。它分发经过修改以便在诸如以下其他操作系统上编译的 `libdispatch`：

- FreeBSD
- Linux
- Solaris
- Windows

如果你需要为 Mac OS X 或 iOS 之外的操作系统编写应用程序，我推荐使用它。

