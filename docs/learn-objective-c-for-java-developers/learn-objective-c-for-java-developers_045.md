# 排版后

`immediately reads what’s available and supplies that in the notification. -readToEndOfFile…` 等待管道关闭，然后发送包含管道中所有剩余数据的通知。

##### `NSStream`

`NSFileHandle`是一个可用于数据流的通用包装器，而`NSStream`是一个专为串行数据流设计的专用类。`NSStream`有两个可用的子类：`NSInputStream`和`NSOutputStream`。它们是最接近`java.io.InputStream`和`java.io.OutputStream`的 Objective-C 等价类。基类`NSStream`定义了两个子类共有的方法（即`-open`、`-close`、`-streamStatus`）。自然地，`NSInputStream`定义了`-read:`...方法，`NSOutputStream`定义了`-write:`...方法，以及其他仅适用于输入或输出流的方法。

与它们的 Java 对应类类似，`NSInputStream`和`NSOutputStream`可以连接到各种源，包括管道、套接字、数据文件和内存缓冲区。但 Objective-C 并未为每种变体定义显式子类，而是提供了一个可以不同模式运行的单一类。你实际使用的对象可能是一个私有子类，但这是一个应被忽略的实现细节。表 13-1 展示了 Java 流类及其对应的`NSInputStream`或`NSOutputStream`构造函数。

**表 13-1. 创建流对象**

| Java | Objective-C |
|------|-------------|
| `new ByteArrayInputStream(bytes)` | `[NSInputStream inputStreamWithData:bytes]` |
| `new FileInputStream(path)` | `[NSInputStream inputStreamWithFileAtPath:path]` |
| `new ByteArrayOutputStream()` | `[NSOutputStream outputStreamToMemory]` |
| `new ByteArrayOutputStream(size)` | `[NSOutputStream outputStreamToBuffer:buffer capacity:size]` |
| `new FileOutputStream(path)` | `[NSOutputStream outputStreamToFileAtPath:path append:NO]` |

特定的 Java 子类包含适用于该类型数据流的方法。例如，`java.io.ByteArrayOutputStream`包含一个`toByteArray()`方法，可将收集到的输出数据作为字节数组对象检索。`NSStream`类通过其属性字典关联流特定信息。要获取由`+outputStreamToMemory`初始化的`NSOutputStream`收集的数据，请获取流的`NSStreamDataWrittenToMemoryStreamKey`属性，如下所示：

```
NSData *bytes = [outStream propertyForKey:NSStreamDataWrittenToMemoryStreamKey];
```

类似地，连接到数据文件的流的当前文件位置可以通过操作流的`NSStreamFileCurrentOffsetKey`属性来检查或修改；该值是一个`NSNumber`对象。这些是仅有的两个通常感兴趣的流属性。其他属性与网络套接字配置有关。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 13 章 远近通信

与 Java 类似，其他服务可能会创建并返回`NSInputStream`或`NSOutputStream`的特化子类——这些子类你不能、也不应尝试自行创建。这些不透明的子类通常是`NSStream`如何连接到管道或如何获取网络套接字端口的方式。

`NSFileHandle`和`NSStream`类之间的一个显著区别是异步数据的处理方式。`NSFileHandle`具有创建临时线程的方法，该线程等待数据可用，然后发送通知。`NSStream`对象设计为在运行循环内工作，提供事件驱动的流处理，而无需创建额外线程。要利用这一点，必须将流附加到正在运行的运行循环，如清单 13-2 所示。

**清单 13-2. `NSStream`事件处理**

```
NSInputStream *inStream = …
MyStreamHandler *delegate = [MyStreamHandler new];
[inStream setDelegate:delegate];
[inStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
                   forMode:NSDefaultRunLoopMode];
[inStream open];
…

@implementation MyStreamHandler
- (void)stream:(NSStream*)stream handleEvent:(NSStreamEvent)eventCode
{
    switch (eventCode) {
        case NSStreamEventHasBytesAvailable:
        {
            uint8_t buffer[1024];
            NSUInteger length;
```



`length = [(NSInputStream*)stream read:buffer maxLength:sizeof(buffer)];`

```objc
if (length!=0) {
// 对 buffer[] 中的数据进行处理……
}
break;
}

case NSStreamEventEndEncountered:
{
// 在流结束时执行操作……
break;
}

case NSStreamEvent…:
…
break;
}
}

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

