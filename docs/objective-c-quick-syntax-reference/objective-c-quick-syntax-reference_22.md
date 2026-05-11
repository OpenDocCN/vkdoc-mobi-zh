# 类方法

**摘要**

在第 16 章中，我们描述了如何使用属性和方法定义类。我们重点介绍的方法是实例方法。实例方法是只能用于对象的方法。当你想使用一个实例方法时，你向一个对象发送消息。

## 类方法的定义

在第 16 章中，我们描述了如何使用属性和方法定义类。我们重点介绍的方法是实例方法。实例方法是只能用于对象的方法。当你想使用一个实例方法时，你向一个对象发送消息。

例如，如果你想向一个 `Project` 对象发送 `generateReport` 消息，你首先需要创建该对象，然后直接将消息发送给该对象。

```
Project *p = [[Project alloc] init];
[p generateReport];
```

类方法与实例方法类似，区别在于它们只能用于类。当你想使用一个类方法时，你必须向该类发送消息。如果你仔细看看上面的构造函数，你会发现你已经在使用一个名为 `alloc` 的类方法了。

### 编写类方法

如果你想创建自己的类方法，你需要从类接口开始。让我们为一个名为 `printTimeStamp` 的类方法编写前向声明，该方法用于打印时间戳。你可以将其添加到上一章开始编写的类中。

```
#import <Foundation/Foundation.h>

@interface Project : NSObject

@property(strong) NSString *name;

-(void)generateReport;

-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date;

+(void)printTimeStamp;

@end
```

这个方法看起来和你已经编写的实例方法类似，除了在返回类型前面有一个加号（`+`）。

下一步是实现这个方法，你必须在 `Project` 的实现中完成。请注意，此处省略了第 16 章的部分代码，以避免示例过长而分心。

```
#import "Project.h"

@implementation Project

+(void)printTimeStamp{
    NSLog(@"Timestamp: %@", [NSDate date]);
}

@end
```

你需要再次使用加号将其定义为类方法，但编码模式与实例方法相同。

当你想使用 `printTimeStamp` 方法时，你直接将消息发送给这个类。你不需要先创建一个对象。

```
[Project printTimeStamp];
```

这条消息会将以下内容打印到控制台日志：

`Timestamp: 2014-10-30 18:13:01 +0000`

