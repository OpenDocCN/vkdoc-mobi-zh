# 第 6 章：集成网络与 Web 服务

## 解析来自 Web 服务的 JSON 与 XML

JSON 和 XML 是解决同一类问题的两种不同方法：我们如何以有意义的方式封装数据，以便发送给另一台计算机？

例如，当你连接天气服务获取十天的预报时，它会返回一个包含这些天最高温和最低温的列表。为了让你的应用能够使用这些数据，Web 服务用于返回数据的格式必须在某处有文档说明，以便你能解析它。但这究竟意味着什么呢？简单来说，你需要在原生`Objective-C`对象和服务器返回的数据之间进行转换。

为了解释这一点，让我们来看一些 XML 格式的数据。这些来自 Yahoo! Web 服务的数据展示了一个两天的预报：

```
<yweather:forecast day="Wed" date="22 Feb 2012" low="31" high="39"
text="Rain/Snow Showers Early" code="5" />
<yweather:forecast day="Thu" date="23 Feb 2012" low="32" high="40" text="PM Snow Showers" code="14" />
```

如你所见，这里有两个`yweather:forecast`对象，它们拥有属性`day`、`date`、`low`、`high`、`text`和`code`。让我们看看同样的数据在 JSON 中是什么样子：

```json
{
    "forecast": [
        {
            "day": "Wed",
            "date": "22 Feb 2012",
            "low": 31,
            "high": 39,
            "text": "Rain/Snow Showers Early",
            "code": 5
        },
        {
            "day": "Thu",
            "date": "23 Feb 2012",
            "low": 32,
            "high": 40,
            "text": "PM Snow Showers",
            "code": 14
        }
    ]
}
```

当 JSON 从服务器接收到时，它是紧凑的，没有换行符，因此它看起来像这样：

```json
{"forecast":[{"day":"Wed","date":"22 Feb 2012","low":31,"high":39,"text":"Rain/Snow Showers Early","code":5},{"day":"Thu","date":"23 Feb 2012","low":32,"high":40,"text":"PM Snow Showers","code":14}]}
```

虽然这两种形式在功能上是相同的，但第一种形式更易于人类阅读。它有一个名为`forecast`的元素数组，每个元素都拥有与 XML 中相同的属性。在深入学习解析过程时，我们将进一步了解每种格式的语法。

### 解析 XML

有两种不同类型的 XML 解析器：流式解析器和树状解析器。

流式解析器（也称为顺序解析器）每次读取一个元素，并在读取时处理该元素的数据。树状解析器则一次性读取整个文档，将元素组织成对象的层次结构。在 iOS 设备上使用树状解析器可能存在风险，因为在应用的整个生命周期中将整个文档保留在内存中可能会触及内存限制。

因此，内置的解析器`NSXMLParser`更接近于流式处理器，尽管它并不被认为是真正的流式解析器。虽然这种方式解析数据可能显得冗长，但在内存受限的环境中它更为安全。

让我们在天气示例中实现`NSXMLParser`。打开你的应用委托的头文件（使用我的类前缀则为`LCTAppDelegate.h`），并通过添加以下加粗的文本，声明它遵循`NSXMLParserDelegate`协议：

```objc
#import <UIKit/UIKit.h>

@interface LCTAppDelegate : UIResponder <NSXMLParserDelegate,
UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@end
```

要理解`NSXMLParser`的工作原理，请考虑一个 XML 元素的结构。一个名称为`element`、内容为`content`的元素，其结构如下：

```
<element>content</element>
```



当 XML 解析器读取文件时，一旦遇到`<element>`标签，它会向代理（delegate）发送一条消息，以此标记元素的开始。解析器会继续读取文本，直到找到`</element>`标签，此时它会向代理发送另一条消息。解析器会保存两个标签之间的文本内容，并通过另一个代理方法将其发送出去。因此，在`NSXMLParserDelegate`中为每个元素定义了三个主要的代理方法，此外还有一个用于捕获错误的方法，以及两个用于标记文档开始和结束的方法：

- `parserDidStartDocument:`
- `parserDidEndDocument:`
- `parser:didStartElement:namespaceURI:qualifiedName:attributes:`
- `parser:didEndElement:namespaceURI:qualifiedName:`
- `parser:foundCharacters:`
- `parser:parseErrorOccurred:`

与优秀的代理协议一样，每个方法的第一个参数都是指向`NSXMLParser`对象的指针。第三和第四个方法还包含用于命名空间和限定名的额外参数，但对于简单的 XML 解析，我们不需要使用它们。如果你进行高级 XML 处理，还有其他方法可用于处理 XML 实体声明等主题。就目前而言，这些方法集合足以让我们解析来自 Yahoo! 的响应。打开你的应用代理实现文件（`LCTAppDelegate.m`），修改`connectionDidFinishLoading:`方法，添加粗体显示的行并删除被划掉的行：

```
- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
    NSLog(@"Response: %@", _receivedResponse);
    NSString *receivedString = [[NSString alloc] initWithData:_receivedData encoding:NSUTF8StringEncoding];
    NSLog(@"Body: %@", receivedString);
    NSXMLParser *parser = [[NSXMLParser alloc] initWithData:_receivedData];
    [parser setDelegate:self];
    [parser parse];
}
```

在这个方法中，我们只需创建一个`NSXMLParser`实例，为其提供从 URL 连接接收到的数据指针，将它的代理设置为`self`（这里指应用代理对象），然后告诉它开始解析。这将解析逻辑的控制权交给 XML 解析器，解析器随后会回调我们的代理方法。

我们要寻找的数据是`yweather:forecast`元素，它没有任何文本内容。相反，它使用属性。一个示例的 forecast 元素如下所示：

```
<yweather:forecast day="Fri" date="24 Feb 2012" low="27" high="35" text="Few Snow Showers" code="14" />
```

属性将作为最后一个参数传递给`parser:didStartElement:namespaceURI:qualifiedName:attributes:`，因此我们可以使用该方法来解析数据。首先，我们创建一个可变数组来传递数据。在文件顶部，将可变数组添加到类的私有实例变量声明中：

```
@implementation LCTAppDelegate {
    NSMutableData *_receivedData;
    NSURLResponse *_receivedResponse;
    NSError *_connectionError;
    NSMutableArray *_forecasts;
}
```

我们将接收到的属性存储在此数组中。接下来，在文件末尾的`@end`编译器指令之前，为`parser:didStartElement:namespaceURI:qualifiedName:attributes:`添加实现。由于所有数据都在属性中，我们只需将字典添加到数组中：

```
- (void)parser:(NSXMLParser *)parser
    didStartElement:(NSString *)elementName
    namespaceURI:(NSString *)namespaceURI
    qualifiedName:(NSString *)qName
    attributes:(NSDictionary *)attributeDict
{
    if ([elementName isEqualToString:@"yweather:forecast"]) {
        [_forecasts addObject:attributeDict];
    }
}
```

我们还需要实现`parserDidStartDocument:`来创建数组。在上一个方法之后，`@end`编译器指令之前添加此方法：

```
- (void)parserDidStartDocument:(NSXMLParser *)parser
{
    _forecasts = [NSMutableArray array];
}
```

现在，当 XML 解析器遇到`yweather:forecast`元素时，我们的代理方法会将属性字典保存到`_forecasts`数组中。



在 `parserDidStartDocument:` 之后实现 `parserDidEndDocument:`，并将数组输出到控制台：

```
- (void)parserDidEndDocument:(NSXMLParser *)parser
{
    NSLog(@"%@", _forecasts);
}
```

构建并运行应用。你应在控制台中看到该数组的描述信息。

输出结果应类似如下：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 6 章：集成网络与 Web 服务**

```
2012-02-24 23:45:50.078 SimpleWeather[1235:f803] (
    {
        code = 14;
        date = "2012 年 2 月 24 日";
        day = 周五;
        high = 35;
        low = 27;
        text = "零星阵雪";
    },
    {
        code = 14;
        date = "2012 年 2 月 25 日";
        day = 周六;
        high = 33;
        low = 21;
        text = "零星阵雪";
    }
)
```

如你所见，数组包含两个天气预报，每个都带有 `code`、`date`、`day`、`high`、`low` 和 `text` 这些键。

**注意：** 虽然控制台输出看起来与 JSON 类似（你将在本章后续内容中更详细地了解 JSON），但它并非 JSON。这只是一种用于日志记录的人类可读输出格式。

我们已成功将 XML 数据解析为 Foundation 对象。此应用的后续步骤可能包括围绕这些结果创建用户界面，例如在表视图或某种自定义视图中显示天气预报。虽然流式 XML 解析器可能复杂，但对于此类简单任务来说，它运行得相当好。然而，大多数新的 Web 服务都使用 JSON，或至少提供 JSON 作为选项。对于使用 Cocoa Touch 的 iOS 开发，你通常会优先使用 JSON 而非 XML。基于此，让我们深入探讨如何解析 JSON 数据。

### 解析 JSON

JSON 是 JavaScript 对象表示法（JavaScript Object Notation）的缩写。它源自 JavaScript 语言，并已成为网络传输对象的首选格式。你在 JSON 中会遇到两种主要结构：数组和字典。数组以 `[` 字符开头，内部元素用逗号分隔，以 `]` 字符结尾。在 JSON 中，数据对象只是字符串和数字，因此一个数字的 JSON 数组可以这样定义：

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 6 章：集成网络与 Web 服务**

```
[1,2,3]
```

字典以 `{` 字符开头，以 `}` 字符结尾。键值对以键开头，后跟 `:` 字符，然后是值，各对之间用逗号分隔。一个简单的字典可以这样定义：

```
{"value1":10,"value2":42}
```

这个字典有两个键：`value1` 和 `value2`，分别对应值 10 和 42。如你所见，JSON 表示法尽可能简单，只用了很少的字符来定义一种简洁的语法。你可能已经在思考如何为 JSON 编写解析器——这可不是一件轻松的事。幸运的是，从 iOS 5 开始，Cocoa Touch 原生支持解析 JSON，所以你无需自己编写！

只需使用 `NSJSONSerialization` 类即可将 JSON 解析为 `NSArray`、`NSDictionary`、`NSString` 和 `NSNumber` 对象。以下是在 URL 连接代理的 `connectionDidFinishLoading:` 方法中解析从 Web 服务返回数据的示例：

```
- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
    NSLog(@"响应: %@", _receivedResponse);

    // _receivedData 是从连接接收到的 NSData 对象
    NSError *parseError = nil;
    id responseObject = [NSJSONSerialization JSONObjectWithData:_receivedData options:0
                                                         error:&parseError];

    // 在此处处理 responseObject
}
```

如你所见，我们使用一个包含从连接接收到的数据的 `NSData` 对象来创建 `responseObject` 对象。根据 JSON 中的内容，`responseObject` 对象将是 `NSArray` 或 `NSDictionary` 中的一种。还可以将一些选项作为第二个参数传递给 `JSONObjectWithData:options:error:`，从而创建 `NSMutableArray` 和 `NSMutableDictionary` 对象（而非不可变版本）、创建 `NSMutableString` 对象（而非 `NSString` 对象），或允许顶层对象不是数组或字典。

### 创建 JSON 表示



