# 文件系统

## 摘要

使用 Objective-C 的亮点之一是能够使用高质量库来访问操作系统提供的大部分资源。在本章中，我将向你展示 Foundation 框架中用于与外部存储文件进行交互的一些类和方法。

在 Objective-C 中，与文件系统交互有多种方式。首先，Foundation 框架提供了一些可用于简化文件读写访问的方法。这些方法已被添加到 `NSString` 和 `NSData` 等类中，提供了一种便捷的方式来访问文件，而无需担心底层存储细节。`NSString` 等对象的内部机制将执行读写数据到磁盘所需的所有步骤。

另一方面，在某些时候，更精细地控制文件访问方式非常重要。例如，大文件可能需要很长时间才能访问。通过使用异步接口，你可以避免此类大型 I/O 延迟。提供这种控制级别是 `NSStream`、`NSInputStream` 和 `NSOutputStream` 等专业化类的目标。它们允许程序员决定文件访问的细节，例如用于传输数据的内部缓冲区大小。还可以暂存数据，仅在应用程序认为必要时才使其对外部文件可用。我将举例说明这些类的工作方式，并开发一个真正异步的数据传输到文件的解决方案。



## 使用 NSURL 指定位置

在 Foundation 框架中访问文件时，通常需要创建一个 `NSURL` 对象，它代表一个单一的 URL。URL（统一资源定位符）是操作系统用于定位特定资源的标识符。URL 不仅能定位文件，还能用于远程资源，例如网页或 FTP 文件。不过，这里我们只关注本地资源。

本地文件的 URL 以 `file://` 开头。例如，要访问位于 `/Users/user1/file.txt` 的文件，你应该使用 URL `file://Users/user1/file.txt`。

Cocoa 中大多数与文件相关的方法都使用 `NSURL` 对象，而不是（或除了）基于字符串的路径名。原因在于，与标准路径相比，`NSURL` 对象具有若干优势。首先，路径名是系统相关的。例如，有些系统使用正斜杠作为分隔符，而另一些则使用反斜杠或冒号。URL 的创建正是为了抽象这些差异。

`NSURL` 提供的另一项服务是，一旦文件被访问，它就能持续追踪该文件。例如，即使文件在文件系统中被移动了，`NSURL` 对象也能继续引用它，尽管其路径已经改变。

最后，URL 不仅限于表示本地文件。你可以使用 URL 通过多种协议来处理远程文件。虽然你可能需要修改代码来处理此类远程资源，但位置的表示形式保持不变。这也是 Cocoa 库尽可能倾向于使用 `NSURL` 的另一个原因：这样，在使用远程资源与本地资源时，无需更改表示形式。

## 读写文本文件

一旦你为要访问的文件找到了正确的位置（通常以 `NSURL` 对象的形式），下一步就是使用与文件系统交互的某个 Cocoa 对象来读取或写入内容。

本章将介绍的第一个对象是 `NSString`。在处理文本文件时，`NSString` 可能是与文件内容交互的最简单方式——除非文件中有一些额外的结构，例如 XML。让我们看一个如何使用名为 `FileReader` 的类通过 `NSString` 从文本文件读取数据的示例。

```
// file FileReader.h

@interface FileReader : NSObject

+ (void) readTest;

- (void) readFile:(NSString*) fileName;

@end
```

这个类中有两个方法：一个实例方法 `readFile:` 和一个类方法 `readTest`。

```
// file FileReader.m

#import "FileReader.h"

@implementation FileReader

- (void) readFile:(NSString*) fileName

{

NSURL *url = [NSURL URLWithString:fileName];

NSError *error;

NSString *str = [NSString

stringWithContentsOfURL:url

encoding:kCFStringEncodingUnicode

error:&error];

NSLog(@"String is %@", str);

}

+ (void) readTest

{

FileReader *reader = [[FileReader alloc] init];

[reader readFile:@"file:///Users/oliveira/testFile"];

}

@end
```

`readFile:` 方法的操作过程如下：首先，它使用 `URLWithString:` 方法创建一个 `NSURL` 对象。一旦创建了 URL 对象，就用它来初始化一个包含其内容的字符串。`stringWithContentsOfURL:encoding:error:` 负责打开 URL 并将其内容传输到 `NSString` 的内部缓冲区。这个方法需要一个编码类型，该类型决定了文件中文本的表示方式。此选项根据编码文本所使用的语言类型而变化。最后，当产生错误条件时，该方法会在其最后一个参数中返回一个错误对象。

注意

`NSString` 也允许从文件路径（而非 URL）读取内容。尽管这很方便，但在大多数情况下，你应该优先使用 `NSURL`。这将有助于保持可移植性，并使你的程序能够使用不同类型的 URL。

类似地，`NSString` 也可以用于将数据写入文本文件。以下代码展示了如何实现一个 `FileWriter` 类：

```
// file FileWriter.h

@interface FileWriter : NSObject

+ (void) writeTest;

- (void) writeFile:(NSString*) fileName content:(NSString*)data;

@end
```

`FileWriter` 类有一个方法，用于将作为第二个参数传入的内容数据写入由参数 `fileName` 定义的文件中。`writeTest` 类方法中也提供了一个测试程序。

```
// file FileWriter.m

#import "FileWriter.h"

@implementation FileWriter

- (void) writeFile:(NSString*) fileName content:(NSString*)data;

{

NSURL *url = [NSURL URLWithString:fileName];

NSError *error;

BOOL res = [data writeToURL:url

atomically:NO

encoding:NSUnicodeStringEncoding

error:&error];

if (!res)

{

NSLog(@"error writing to file %@", [error userInfo]);

}

}

+ (void) writeTest

{

FileWriter *writer = [[FileWriter alloc] init];

[writer writeFile:@"file:///Users/oliveira/testFile2"

content:@"This is the content of the file written by"

" the file writer."];

}

@end
```

你可以很快看出，`writeFile:content:` 方法只是 `NSString` 功能的一个封装。`writeToURL:atomically:encoding:error:` 方法负责获取存储在 data 变量中的文本，并将其写入由给定 `NSURL` 对象确定的位置的文件中。参数 `atomically` 决定了该过程是否由文件系统以原子方式执行。为了保证原子性，Cocoa 使用一个缓冲区来存储数据，直到整个文件写入完毕，然后将文件重命名为其目标 URL。`encoding` 参数与你之前读取文件时看到的编码类似。最后，`error` 参数仅在需要报告错误条件时使用。



## 读写二进制数据

上面的示例展示了如何操作文本文件。在这些情况下，使用 `NSString` 来处理文件内容并进行读写非常容易。然而，大多数应用程序还需要访问二进制格式的文件，例如压缩文件、图像以及其他无法用文本编辑器直接修改的专有格式。

对于小文件，你可以使用与 `NSString` 相对应的二进制数据处理类：`NSData`。为了简化操作此类二进制文件的过程，Cocoa 为 `NSData` 启用了类似 `NSString` 的方法，使其能够读写磁盘上的数据。

读取二进制文件时，需要提供一个用于临时存储数据的缓冲区，以及在数据读取完成后检索数据的途径。`NSData` 默认实现了这一功能，留给程序员的仅剩处理返回数据的任务。

以下示例展示了其工作原理：你将编写一个类来读取数据，并在日志中打印其部分字节，代码如下：

```
// 文件 ReadBinaryData.h

@interface ReadBinaryData : NSObject

- (NSData*)readData:(NSString*)fileName;

+ (void) testRead;

@end
```

该接口仅包含两个方法：`readData:`，它接受你想要读取的文件名作为参数；以及 `testRead`，一个用于测试 `readData:` 功能的类方法。

```
// 文件 ReadBinaryData.m

#import "ReadBinaryData.h"

@implementation ReadBinaryData

#define MAX_DATA_SIZE 1024

- (NSData*)readData:(NSString*)fileName

{

NSURL *url = [NSURL URLWithString:fileName];

NSError *error;

NSData *data = [NSData dataWithContentsOfURL:url options:NSDataReadingUncached error:&error];

if (data == nil)

{

NSLog(@"读取文件时出错: %@", [error userInfo]);

}

else

{

int data_size = (int)[data length];

unichar array[MAX_DATA_SIZE];

int size = data_size;

if (data_size > MAX_DATA_SIZE) size = MAX_DATA_SIZE;

[data getBytes:array length:size];

NSString *dataStr = [NSString stringWithCharacters:array length:size];

NSLog(@"数据的首字节为: %@", dataStr);

}

return data;

}

+ (void) testRead

{

ReadBinaryData *reader = [[ReadBinaryData alloc] init];

[reader readData:@"file:///Users/oliveira/testFile"];

[reader release];

}

@end
```

在实现文件的第一部分，你定义了常量 `MAX_DATA_SIZE`，用于确定你想从文件中显示的最大字符数。

`readData:` 方法创建了一个包含文件名的 `NSURL` 对象，然后将它作为参数传递给 `dataWithContentsOfURL:options:error:` 方法。该方法的第一个参数是文件 URL，紧接着是一个选项，用于决定文件是否会被缓存或进行内存映射等。最后一个参数 (`error`) 是一个指向 `NSError` 对象的指针，仅在检测到失败时使用。

如果 `dataWithContentsOfURL:options:error:` 返回的数据有效，你便可以检索其前一小部分数据并打印到日志窗口。这可以通过 `getBytes:length:` 方法实现，它是 `NSData` 的一个方法，利用一个辅助内存数组返回存储的字节，数组长度由第二个参数决定。你使用了一个简单的字符数组，并且仅复制了开头几个字符用于测试。在实践中，你需要通过一次或多次迭代来检索 `NSData` 中存储的所有数据，并对其进行有意义的处理。

`testRead` 方法用于检验 `ReadBinaryData` 的功能。你创建了一个该类的对象，然后向其发送一条 `readData:` 消息，参数是存储在你的主目录中的一个示例文件的名称。执行后，文件的部分内容应被打印到应用程序日志中。

`NSData` 也能像 `NSString` 一样将数据写入文件。为避免重复类似的示例，我将直接展示 `writeData:` 方法的内容，该方法使用 `NSData` 将一个字节数组输出到二进制文件：

```
- (void)writeData:(int*)numbers length:(int)size

dest:(NSString*)fileName

{

NSURL *url = [NSURL URLWithString:fileName];

NSError *error;

NSData *data = [NSData dataWithBytes:numbers length:sizeof(int)*size];

BOOL ok = [data writeToURL:url

options:NSDataWritingAtomic

error:&error];

if (!ok)

{

NSLog(@"写入文件时出错: %@", [error userInfo]);

}

}
```

这一次，你创建了一个 `NSData` 对象来包含作为参数传递给 `writeData:length:dest:` 方法的整数数组。要存储的数据长度由 `sizeof(int)*size` 确定，它给出了存储该数组数据所需的字节数。

下一步是使用 `NSData` 的 `writeToURL:options:error:` 方法将数组写入磁盘。该方法有一个 `options` 参数，可用于决定文件写入磁盘的方式。在本例中，我要求以原子方式写入文件，即所有字节先写入一个临时文件，然后在写入完成后移动到正确位置。如果在执行该方法时发生错误，`error` 变量将用于存储描述错误信息的消息。

## 使用 NSStream

使用 `NSString` 和 `NSData` 操作文件虽然方便，但并非可扩展的解决方案。这些简单类的一个问题是，程序员对 I/O 过程的控制力不足。但这种文件访问模式的最大问题在于，`NSString` 和 `NSData` 执行的是同步操作：也就是说，应用程序的当前线程必须等待整个文件处理完毕。这对于小文件和简单的应用程序或许可以接受，但当文件体积增大或需要快速响应时，这种方式就无法扩展了。

因此，在许多情况下，使用 `NSString` 或 `NSData` 进行文件访问变得不切实际。Cocoa 通过 `NSStream` 类及其相关类，提供了一种更好地控制文件操作的方式。`NSStream` 能更好地控制文件的访问方式，并且能够将工作分割成更小的块，使应用程序能够处理而不产生长时间停顿。

`NSStream` 有一个通用接口，由 `NSInputStream` 和 `NSOutputStream` 实现。这两个类共享相同的基于委托的文件访问模型。本质上，当使用 `NSStream` 时，你需要定义一个委托对象，该对象将在执行 I/O 操作时被调用。

`NSStream` 的另一个有趣之处在于，它不仅限于文件访问。其内部模型也可应用于任何 URL，例如网络资源或 FTP 文件。它还可用于直接连接网络套接字，尽管这种应用超出了本章的范围。然而，你所学习的一切（稍作修改）都可以用于访问客户端/服务器应用程序中使用的网络套接字。



### 读取文件

要使用 `NSStream` 读取文件，你需要设置一个 `NSInputStream`，然后建立一个委托，该委托将在数据可用时进行读取。这种接口通过使用异步机制来避免大 I/O 延迟的问题，该机制仅在数据可供消费时触发事件。

使用 `NSInputStream` 的第一步是定义负责接收数据读取接口触发的事件的对象。通常，事件处理代码会被添加到你的主委托对象中，但你可能希望为此创建一个专用的数据处理类。

让我们创建一个名为 `AsyncDataReader` 的类，该类使用 `NSInputStream` 读取文件。该类的接口有一个方法来设置流，另一个方法来处理由 `NSStream` 对象触发的事件。

```
// 文件 AsyncDataReader.h

@interface AsyncDataReader : NSObject <NSStreamDelegate>
{
    NSInputStream *_stream;
    NSMutableData *_data;
}

- (void)setupStream:(NSString*)path;
- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)event;
+ (void)testRead;

@end
```

该接口实现了 `NSStreamDelegate` 协议，这对于想要消费 `NSStream` 发送的数据的对象来说是必需的。该类包含两个实例变量：一个用于读取输入文件的 `NSInputStream`，以及一个用于在内存中存储文件内容的 `NSMutableData` 对象（`NSMutableData` 是 `NSData` 的子类，允许对其内容进行添加或删除）。接口中有三个方法。`setupStream:` 方法负责对输入流进行初始设置；它接收文件名作为其唯一参数。`stream:handleEvent:` 方法是 `NSStreamDelegate` 协议所要求的；例如，当有新的数据可供消费时，就会调用此方法。最后，一个名为 `testRead` 的类方法演示了如何使用这个类。

```
// 文件 AsyncDataReader.m

#import "AsyncDataReader.h"

@implementation AsyncDataReader

- (void) setupStream:(NSString*)fileName
{
    NSURL *url = [NSURL URLWithString:fileName];
    if (_stream == nil)
        _stream = [[NSInputStream alloc] initWithURL:url];
    [_stream setDelegate:self];
    [_stream scheduleInRunLoop:[NSRunLoop currentRunLoop]
                       forMode:NSDefaultRunLoopMode];
    [_stream open];
}
```

这是类实现的第一部分，你需要在此处设置用于读取操作的流。首先，你检查 `_stream` 变量是否有效，如果无效，则使用 `alloc` 后跟 `initWithURL:` 来创建对象。下一步是为 `NSStream` 设置委托，在本例中是当前的 `AsyncDataReader` 类。

`NSInputStream` 使用消息传递机制与委托对象进行通信。为此，有必要将流对象添加到当前线程的运行循环中。这是通过 `scheduleInRunLoop:forMode:` 消息实现的。要找到当前的运行循环，你可以使用 `NSRunLoop` 类的 `currentRunLoop` 方法。然后通过 `NSDefaultRunLoopMode` 选择标准操作模式。

最后，为了开始处理输入流，你需要向流对象发送 `open` 消息。这将执行打开文件并开始读取其中存储的字节所需的全部初始化步骤。

```
- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)event
{
    uint8 bytes[256];
    switch (event)
    {
        case NSStreamEventHasBytesAvailable:
        {
            if (_data == nil)
                _data = [[NSMutableData alloc] init];
            NSInteger len = [(NSInputStream*)stream read:bytes maxLength:256];
            [_data appendBytes:bytes length:len*sizeof(bytes[0])];
            NSLog(@"收到数据：%ld 字节：", len);
            // 保存存储在 bytes 中的数据
        }
        break;

        case NSStreamEventEndEncountered:
        {
            [_stream close];
            [_stream removeFromRunLoop:[NSRunLoop currentRunLoop]
                               forMode:NSDefaultRunLoopMode];
            [_stream release];
            _stream = nil;
        }
        break;

        default:
            break;
    }
}
```

`stream:handleEvent:` 方法是这个类的核心，因为它负责解释由 `NSInputStream` 委派的每一条消息。`NSStreamDelegate` 可以接收到多种类型的消息，但你只需关注两种类型：`NSStreamEventHasBytesAvailable`，表示有新数据可供消费；以及 `NSStreamEventEndEncountered`，用于指示流结束。对于大多数只想读取文件内容的情况，这两个消息就足够了。

响应 `NSStreamEventHasBytesAvailable` 事件时，你准备好 `_data` 实例变量来存储接收到的字节。如果 `_data` 尚未初始化，你将使用 `alloc`/`init` 这对消息来创建一个新的 `NSMutableData` 对象。接下来，你需要将数据读入一个内部缓冲区，并确定接收到的字节数。这是通过调用 `read:maxLength:` 方法完成的。此方法会将内部存储的数据传输到 `bytes` 数组中。你需要指明缓冲区的最大长度，以便流只会复制你可以在声明的缓冲区中存储的字节数量。

> **注意：** 在设置每个事件处理的条目数量（上例中的缓冲区大小）时，你有机会影响应用程序的性能。较小的缓冲区会迫使 `NSStream` 发送更多消息，直到文件完全传输完毕，这可能会导致性能下降。另一方面，太大的缓冲区可能需要很长时间来处理，并可能导致用户界面延迟。你应该根据对应用程序运行效果的实验，来确定内部缓冲区的最佳大小。

`stream:handleEvent:` 方法中的下一步是将接收到的数据添加到 `_data` 对象中，以便在需要时其他方法可以使用这些数据。缓冲区的长度使用 `read:maxLength:` 方法的结果来确定。为了调试目的，此信息也会打印到日志中。

`stream:handleEvent:` 处理的第二个消息是 `NSStreamEventEndEncountered`，当流关闭且没有更多数据时，此消息会发送给委托对象。每个应用程序都可以定义如何响应此事件，但在大多数情况下，需要释放流对象。你需要首先通过向 `_stream` 实例变量发送 `close` 消息来关闭流。然后，你需要将流从消息循环中移除。这是通过使用 `removeFromRunLoop:forMode:` 方法完成的。最后，你可以对 `NSStream` 对象调用 `release` 以释放其内容。你还需要将 `_stream` 变量设置为 `nil`，以便以后可以重用，而无需冒险调用已释放的对象。

```
- (void) dealloc
{
    [_data release];
    [super dealloc];
}
```

在 `dealloc` 方法中，你释放用于存储从 `NSInputStream` 传输的字节的 `_data` 对象。到这个时候，该类应该已经使用了这些数据（尽管这里没有展示使用 `_data` 的方法）。

```
+ (void) testRead
{
    AsyncDataReader *reader = [[AsyncDataReader alloc] init];
    [reader setupStream:@"file:///Users/oliveira/testFile"];
}

@end
```

实现部分的最后部分是 `testRead` 方法。你使用标准的 `alloc`/`init` 序列来创建一个读取器。然后，你向其发送 `setupStream:` 消息，并将要读取的测试文件的 URL 路径作为参数传递。这样做的结果是，`NSInputStream` 会被创建并启动。读取器对象将收到指示重要事件的通知，例如数据的可用性和流的结束。你应该会在日志中看到表明数据正在被接收和读取的消息。



### 使用 `NSOutputStream` 写入文件

使用 `NSOutputStream` 写入文件与使用 `NSInputStream` 读取文件类似。你仍然采用基于事件的策略与流内容交互，并通过实现 `NSStreamDelegate` 协议的委托来订阅事件。不过，`NSOutputStream` 有其自身类型的消息需要由委托处理。需要执行的任务包括初始化流以及向流发送新数据。

以下代码片段展示了使用 `NSOutputStream` 异步将数据写入文件的类的接口。

```
// File AsyncDataWriter.h

@interface AsyncDataWriter : NSObject <NSStreamDelegate>
{
    NSOutputStream *_outStream;
    int _position;
}

- (void) setupOutputStream:(NSString*)fileName;
- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)event;
- (void)dealloc;
+ (AsyncDataWriter*)testWrite;

@property (retain) NSArray *outData;

@end
```

该类包含一个指向 `NSOutputStream` 的指针和一个用于确定输出数据数组当前位置的整数。它有三个实例方法：一个用于设置输出流，另一个用于接收发送给 `NSStreamDelegate` 协议的消息，第三个是标准的 `dealloc` 方法。最后，你还有一个 `testWrite` 类方法，用于展示如何使用该类。

`setupOutputStream` 方法负责初始化流和使用的数据。我使用字符串作为测试数据，并存储在 `outData` 属性中。不过，很容易看出如何为可能存储在内存中的任何数据集做到这一点。

```
- (void) setupOutputStream:(NSString*)fileName
{
    NSURL *url = [NSURL URLWithString:fileName];

    if (_outStream == nil)
    {
        _outStream = [[NSOutputStream alloc]
                      initWithURL:url
                      append:YES];
        self.outData = @[
            @"A string\n",
            @"B values\n",
            @"C test\n",
            @"D class\n" ];
        _position = 0;
    }

    [_outStream setDelegate:self];
    [_outStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
                         forMode:NSDefaultRunLoopMode];
    [_outStream open];
}
```

每当变量 `_outStream` 为 `nil` 时，便会执行 `setupOutputStream` 的初始化。第一步是创建一个输出流，加载基于作为参数传递的文件名创建的 URL。接下来，创建一个包含测试数据的数组，这些数据稍后将用于提供输出文件的内容。

你创建了一个 `releaseStream` 方法，当数据完全传输到输出文件时调用该方法。

```
- (void)releaseStream
{
    [_outStream close];
    [_outStream removeFromRunLoop:[NSRunLoop currentRunLoop]
                         forMode:NSDefaultRunLoopMode];
    [_outStream release];
    _outStream = nil;
}
```

在此代码中，首先关闭输出流，以便不再接受额外数据进行输出。然后，将该流从 `currentRunLoop` 中移除；因此，委托对象将不再收到针对此特定输出流的后续事件请求。接着，释放该流并将其设置为 `nil`，以便后续可用于写入另一个文件。

与 `NSInputStream` 类似，`stream:handleEvent:` 方法是该类工作的核心部分。在该方法中，每当数据可供应用程序使用时，你将提供数据。因此，输出流将使用提供的数据写入创建流时命名的文件。

```
- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)event
{
    char bytes[NUM_ITEMS_IN_ARRAY];

    switch (event)
    {
        case NSStreamEventHasSpaceAvailable:
            if (_position < [_outData count])
            {
                NSString *element = _outData[_position];
                [element getCString:bytes
                         maxLength:NUM_ITEMS_IN_ARRAY
                          encoding:NSASCIIStringEncoding];
                NSLog(@"ss %s %d", bytes, (int)[element length]);
                [_outStream write:(const uint8_t *)bytes
                       maxLength:[element length]];
            }
            else
            {
                [self releaseStream];
            }
            position++;
            break;

        case NSStreamEventEndEncountered:
            [self releaseStream];
            break;

        default:
            break;
    }
}
```

首先，你声明一个用于临时数据传输的缓冲区。然后，如果事件是 `NSStreamEventHasSpaceAvailable`，这意味着你需要提供额外的数据（如果有）。在上面的代码中，通过测试 `_position` 是否小于或等于 `outData` 中的元素数量，检查 `outData` 数组中是否存在有效位置。如果存在有效位置，则开始获取相应的 `NSString`。然后，使用方法 `getCString:maxLength:encoding:` 从 `NSString` 中获取一个 C 字符串（字符数组）。一旦数据被复制到 `bytes` 数组中，便向 `_outStream` 对象发送 `write:maxLength:` 消息，以便将原始数据传输到输出文件。

**注意：** 使用上述示例时，请注意我使用了常量 `NUM_ITEMS_IN_ARRAY` 来简化演示。在实际操作中，你需要做好准备并分配足够的内存来处理任意大小的数据。

如果没有更多可用数据，则调用前面描述的 `releaseStream` 方法。当收到的消息是 `NSStreamEventEndEncountered` 时，你也需要释放该流，因为这表示无法再向输出文件添加更多数据。

最后，以下是使用上述类的方法的方式：

```
+ (AsyncDataWriter*)testWrite
{
    AsyncDataWriter *writer = [[AsyncDataWriter alloc] init];
    [writer setupOutputStream:@"file:///Users/oliveira/testFile4"];
    return [writer autorelease];
}
```

`testWrite` 方法是一个类方法，可以直接调用来测试类的行为。第一步是创建一个新的 `AsyncDataWriter` 实例。然后，向 writer 发送 `setupOutputStream:` 消息。这将设置流并尝试打开它。之后，流对象将开始向 `AsyncDataWriter` 对象发送事件，并最终将数据传输到输出文件。

尽管使用 `NSStream` 及其子类读取或写入数据并非直截了当，但它为程序员提供了极大的灵活性。首先，`NSStream` 的用途具有适应性：它可以用于读写文件、网络套接字或内存缓冲区。因此，你上面编写的代码只需稍作改动即可应用于许多场景。`NSStream` 的另一个优势是其异步行为。使用 `NSStream` 的应用程序不会因数据输入/输出问题而产生任何延迟，即使在处理大文件时也是如此。这是因为 `NSStream` 会将工作分解为小任务，这些任务会作为应用程序运行循环的一部分来执行，在该循环中所有消息（包括 UI 消息）都会被处理。因此，应用程序不会在等待这些 I/O 任务完成时停止，而是会继续处理其他事件。




## 总结

本章介绍了在 Objective-C 中如何从文件读写数据。首先，你了解了文件路径在 Foundation 框架中的表示方式。`NSURL` 作为一个标准且通用的类，目前是编码文件路径的首选方式。

你学习了如何使用 Foundation 框架中的基本类来读取简单文件。该框架的设计使得常见任务（如读取小文件）无需过多开销即可完成。事实上，使用 `NSString` 接口只需调用单个方法即可读取或写入文件。同样，也可以使用 `NSData` 类读写二进制文件，该类允许将任何数据格式存储在内存中。

你还了解了 `NSStream` 类及其具体实现 `NSInputStream` 和 `NSOutputStream`。这些类可用于创建文件系统的异步接口，也常与网络套接字或其他网络资源进行通信。在本章中，你看到了使用 `NSStream` 对象从外部文件读写数据所需的代码。

通过本章的学习，你已经掌握了 Objective-C 中一些最基础的技术。现在，你已经清楚理解面向对象编程的工作原理，以及该语言提供的库如何支持这种编程风格。下一章将作为参考，总结你所了解的关于 Foundation 框架的信息，涵盖你在基于 Cocoa 的应用程序中会用到的大多数重要类。

