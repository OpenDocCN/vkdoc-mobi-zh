# 排版后的文档

`+connectionWithRequest:delegate:`便利构造器创建了一个`NSConnection`对象，并立即发起请求。响应通过一系列事件传达给委托对象。`NSURLConnection`必须在活动运行循环的上下文中创建。

**提示** 当从`NSURLRequest`创建`NSURLConnection`时，连接会深拷贝请求对象。后续对请求的修改不会影响连接。`NSURLRequest`对象可以立即修改并用于创建另一个连接，而不会影响任何先前创建的连接。

一旦初始标头和元数据被读取并组装成`NSURLResponse`对象，委托会收到`-connection:didReceiveResponse:`消息。随后，零次或多次`-connection:didReceiveData:`消息将剩余内容传递给委托。最后，`-connectionDidFinishLoading:`消息表示事务完成。在任何时候，收到`-connection:didFailWithError:`消息表示请求未成功完成，并说明原因。

如果服务将请求重定向到另一个 URL（这本质上会重新启动连接），则可能会收到多次`-connection:didReceiveResponse:`消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 13 章 ■ 近远程通信

##### 写入 URL

到目前为止，只处理了最基本的请求。要定义更复杂的请求，或向请求包含额外数据，需要更细粒度的对象构造。清单 13-12 展示了一个示例，该示例将包含表单数据的字节数组发布到 HTTP 服务器。

**清单 13-12\. 向 HTTP 服务器发布表单数据**

**Java**

```java
public byte[] submitForm( byte[] formData )
{
    byte[] response = null;
    try {
        URL url = new URL("http://localhost/form.jsp");
        HttpURLConnection connection = (HttpURLConnection)url.openConnection();
        connection.setDoOutput(true);
        connection.setRequestMethod("POST");
        // (configure any additional headers or properties here)
        OutputStream requestStream = connection.getOutputStream();
        requestStream.write(formData);
        InputStream responseStream = connection.getInputStream();
        ByteArrayOutputStream buffer = new ByteArrayOutputStream();
        int c;
        while ( (c=responseStream.read()) != -1)
            buffer.write(c);
        response = buffer.toByteArray();
    } catch (Exception e) {
        e.printStackTrace();
    }
    return (response);
}
```

**Objective-C**

```objective-c
- (NSData*)submitForm:(NSData*)formData
{
    NSURL *url = [NSURL URLWithString:@"http://localhost/form.jsp"];
    NSMutableURLRequest* urlRequest = [NSMutableURLRequest requestWithURL:url];
    [urlRequest setHTTPMethod:@"POST"];
    [urlRequest setHTTPBodyStream:[NSInputStream inputStreamWithData:formData]];
    // (configure any additional headers or properties here)
    NSURLResponse *response = nil;
    NSData *responseData = [NSURLConnection sendSynchronousRequest:urlRequest
                                                returningResponse:&response
                                                            error:NULL];
    return responseData;
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 13 章 ■ 近远程通信

Objective-C 和 Java 之间的两个显著区别如下：

- 在 Java 中，`URLConnection`对象用于配置请求。在 Objective-C 中，你需要创建`NSMutableURLRequest`来自定义请求。
- Java 的`URLConnection`创建连接到请求数据流和响应数据流的`OutputStream`和`InputStream`对象。Objective-C 则完全相反。它不是获取`OutputStream`来写入请求数据，而是提供一个输入流对象，`NSURLConnection`将读取该对象以获取请求数据。

##### 下载 URL

Cocoa 框架提供了一个便捷的实用类，专门用于从 URL 下载文件。

`NSURLDownload`对象的构造方式与`NSURLConnection`对象非常相似，但除了请求对象外，还需要提供目标文件路径。下载将自动启动，并根据成功与否向你的委托对象发送`-downloadDidFinish:`消息。



-   否则会收到`download:didFailWithError:`消息。一个简单下载器类的代码如代码清单 13-13 所示。

## 代码清单 13-13\. 简易文件下载器类

```
@interface FileDownloader : NSObject

- (void)downloadURL:(NSString*)source toPath:(NSString*)destination;

@end

@implementation FileDownloader

- (void)downloadURL:(NSString*)source toPath:(NSString*)destination
{
    NSURL *url = [NSURL URLWithString:source];
    NSURLRequest *urlRequest = [NSURLRequest requestWithURL:url];
    NSURLDownload *download = [[NSURLDownload alloc] initWithRequest:urlRequest delegate:self];
    [download setDestination:destination allowOverwrite:YES];
    // 下载自动开始
}

- (void)download:(NSURLDownload*)download didFailWithError:(NSError*)error
{
    // 下载失败
}

- (void)downloadDidFinish:(NSURLDownload*)download
{
    // 下载成功完成
}

@end
```

`NSURLDownload` 还支持许多其他委托方法，用于监控下载进度、响应身份验证挑战以及交互式地决定目标文件路径。它还通过 `-initWithResumeData:delegate:path:` 构造器支持恢复中断的下载。

## 缓存和 Cookie

与 Java 类似，Objective-C 的 URL 连接支持本地缓存和 Cookie。在 Java 中，通过`java.net.URLConnection.setUseCaches(boolean)`配置缓存，Cookie 则自动处理。

Objective-C 提供了一个进程级缓存管理器，可将频繁请求的数据保存在内存或磁盘中以提高性能。你可以通过两种方式影响请求对缓存的使用：一是在创建`NSURLRequest`时指定`NSURLRequestCachePolicy`，二是在发送请求前设置`NSMutableURLRequest`的策略。策略可以选择使用最佳默认设置（推荐），或者从多种策略中选取，例如忽略所有缓存数据或仅响应缓存数据。更复杂的缓存管理可以通过在`NSURLConnection`委托对象中实现`-connection:willCacheResponse:`方法来完成。在将接收到的任何数据存入缓存之前，会向委托发送此消息。委托可以允许原样存储、阻止存储，或提供修改后的数据替代存储。

Cookie 由系统范围的 Cookie 存储服务自动提供。默认情况下，会根据用户选择的安全策略处理 Cookie。你可以通过创建`NSMutableURLRequest`对象并发送`[request setHTTPShouldHandleCookies:NO]`来禁用单个请求的 Cookie。你也可以更改 Cookie 策略，但这会改变系统上所有进程的策略。

### 总结

Objective-C 提供了多种通信技术，反映了现代应用面临的各种通信问题。从简单的通知到分布式对象，你选择的解决方案取决于你要解决的问题类型。在大多数情况下，Objective-C 的解决方案与你熟悉的 Java 方案相似，尽管实现细节可能有显著差异。

---

## 第 14 章：异常处理

Objective-C 的异常处理能力与 Java 几乎相同。自然地，Objective-C 对异常的处理更加随意，没有 Java 那样的严格要求。你可以像在 Java 中一样使用 Objective-C 异常来设计和编写代码，也可以完全忽略它们，或者采取折中方案。本章将简要比较 Objective-C 和 Java 异常处理的相似之处（两者有很多共同点），然后解释一些细微的差异。

后续章节将讨论断言和异常的替代方案。

### 使用异常

在 Objective-C 和 Java 中，创建、抛出和捕获异常的方式几乎相同。代码清单 14-1 展示了一些简单的异常处理。

### 代码清单 14-1\. 异常处理

**Java**

```
public class Tosser
{
    public void catcher( ) throws Exception
    {
        try {
            System.out.println("Tosser.catcher(): trying");
            thrower();
        } catch ( SpecificException se ) {
            System.out.println("caught SpecificException: "+se);
        } catch ( Exception e ) {
            System.out.println("caught Exception: "+e);
            throw e;
        } finally {
            System.out.println("Tosser.catcher(): finished");
        }
    }

    public void thrower( ) throws Exception
    {
        throw new Exception("thrower() does not work");
    }
}
```

**Objective-C**

```
@interface Tosser : NSObject
- (void)catcher;
- (void)thrower;
@end

@implementation Tosser
- (void)catcher
{
    @try {
        NSLog(@"%s trying",__func__);
        [self thrower];
    } @catch ( SpecificException *se ) {
        NSLog(@"caught SpecificException: %@",se);
    } @catch ( NSException *e ) {
        NSLog(@"caught NSException: %@",e);
        @throw e;
    } @finally {
        NSLog(@"%s finished",__func__);
    }
}

- (void)thrower
{
    @throw [NSException exceptionWithName:@"MyException"
                                  reason:@"-thrower does not work"
                                userInfo:nil];
}
@end
```

两者的相似之处远比差异更为显著。具体来说，`@try`、`@catch`和`@finally`块与 Java 中的`try`、`catch`和`finally`块具有相同的语法、顺序和执行流程。你可以像使用 Java 的`throw`语句一样，使用 Objective-C 的`@throw`指令抛出异常对象。捕获的异常可以以相同方式进行处理或重新抛出。

如果你希望像在 Java 中那样在 Objective-C 中使用异常，完全可以。Objective-C 异常的能力与 Java 非常接近，你很可能不会注意到任何显著差异。然而，确实存在一些差异——主要在于 Objective-C 允许而你 Java 不允许的操作。下一节将详细解释这些差异以及它们可能对你的设计产生的影响。

## 异常处理差异

Objective-C 对异常的处理比 Java 宽松得多。有人可能会认为它过于宽松，但这属于学术讨论。Java 与 Objective-C 异常处理的大多数差异都超出了 Java 的允许范围。

### 无捕获或声明要求

Java 语言有“捕获或声明”规则，要求你要么声明方法可能抛出的异常，要么在方法体内捕获这些异常。Objective-C 不会检查你的代码是否捕获了异常，甚至没有用于声明方法抛出异常的语法。这主要是因为（由于类方法的动态特性）它无法预知这些信息。用 Java 的术语来说，所有 Objective-C 异常都属于未检查异常。

### 可抛出任意对象

Objective-C 可以抛出或捕获任何对象，而 Java 要求`throw`语句的对象必须实现`Throwable`接口。如果仅仅一个`NSString`对象就足以传达你关于异常的所有信息，那么以下代码完全有效：

```
@throw @"出了点问题";
```

例如，当`NSOperation`失败时，你的代码可以抛出一个异常。但与其抛出一个引用该`NSOperation`的`NSException`，你不如直接抛出`NSOperation`对象本身。具体抛出什么当然取决于你想要实现的目标，但你不必局限于`NSException`及其子类。



> **注意**  
说到子类，`NSException` 的子类非常少。在 Java 中，你可以通过继承 `java.lang.Exception` 来创建不同类型的异常。而 `NSException` 对象有一个 name 属性，通常通过名称而非类名来识别异常类型。这并不是说你不应该继承 `NSException`。我经常这样做，但仅限于是为了向 `NSException` 对象添加功能，或者创建一个大类别的异常。Cocoa 框架定义了一些常用的异常名称，你可以在 *Cocoa 异常编程主题介绍*[¹] 中找到。

如果你可以抛出任何类型的对象，你可能会好奇 `NSException` 类具体扮演什么角色。首先，`NSException` 是专门为传达异常细节而构建的。它包含有用的信息，例如堆栈跟踪和异常描述，并且可以参与本节后面介绍的非处理异常机制。最初，`NSException` 是抛出异常的唯一机制。详细信息请参见本章末尾的 *旧版异常* 一节。正因为如此，许多较旧的框架只能捕获 `NSException` 类型的异常。如果你抛出的对象可能不会被你的代码捕获，请确保它是一个 `NSException` 对象。

[¹]: Apple, Inc., *Cocoa 异常编程主题介绍*, [`developer.apple.com/documentation/Cocoa/Conceptual/Exceptions/`](http://developer.apple.com/documentation/Cocoa/Conceptual/Exceptions/), 2007.

---

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 14 章 ■ 异常处理

### 重抛异常

Objective-C 包含一种便捷的语法，用于重抛在 `@catch` 块中捕获的异常，如代码清单 14-2 所示。

**代码清单 14-2.** 重抛异常

```
@try {
    …
} @catch (NSException *exception) {
    …
    @throw;
}
```

一个空的 `@throw` 指令会重抛其所在 `@catch` 块捕获的异常。在代码清单 14-2 中，`@throw` 语句等同于 `@throw exception`。

#### 捕获顺序

与 Java 类似，`@catch` 块的顺序必须从最具体到最通用。换句话说，捕获特定类的 `@catch` 块不能跟在捕获该类的子类的 `@catch` 块之后。`@catch` 块按顺序依次测试，第一个 `@catch` 块会阻止后续 `@catch` 块执行。

在 Java 中，顺序不当的 `@catch` 块会导致编译器致命错误。而 Objective-C 编译器只会发出警告——并且只有在知道类的继承关系时才能发出该警告。

以代码清单 14-3 中的代码为例。

**代码清单 14-3.** 顺序不当的 `@catch` 块

```
@class MysteryException
…
@try {
    …
} @catch (NSObject *anything) {
    …
} @catch (MysteryException *exception) {
    …
}
```

第二个 `@catch` 块永远不会被执行，因为 `MysteryException` 是 `NSObject` 的子类。但这段代码不会产生编译器警告。这是因为 `@class` 语句只让编译器知道 `MysteryException` 类存在，但未说明其任何信息。作为规则，对于你打算捕获的所有对象，都应包含其类定义。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 14 章 ■ 异常处理

### 链式处理

`NSException` 类没有像 `java.lang.Throwable` 那样的 cause 属性。相反，`NSException` 对象包含一个字典（映射）对象，允许你为异常提供任意上下文信息。如果你想抛出一个引用另一个异常的异常，可以将该异常（连同任何其他相关细节）包含在异常的信息字典中，如代码清单 14-4 所示。

**代码清单 14-4.** 链式异常

**Java**

```
try {
    …
} catch (Exception e) {
    throw new SpecificException("encountered exception", e);
}
```

**Objective-C**

```
@try {
    …
} @catch (NSException *exception) {
    NSDictionary *userInfo = [NSDictionary dictionaryWithObject:exception
                                                        forKey:@"RootCause"];
    @throw [SpecificException exceptionWithName:@"MySpecialException"
                                        reason:@"encountered exception"
                                      userInfo:userInfo];
}
```

#### 调用堆栈



