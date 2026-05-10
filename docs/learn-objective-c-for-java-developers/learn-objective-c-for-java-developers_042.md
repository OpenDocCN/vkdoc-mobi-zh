# 序列化中的向后兼容性与类替换

## 类版本控制

在顺序归档中，向后兼容性是通过类版本（class version）实现的，这种方式与 Java 有些类似。在 Java 中，每个类都会被自动分配一个类版本——这相当于该类定义的哈希码。默认情况下，Java 运行时仅当对象的版本与创建序列化数据时使用的版本完全匹配时，才会重构该对象。因此，对类的任何更改都会使其与之前版本的序列化数据不兼容。这是极其严格的限制，但非常安全。程序员可以通过为类声明一个永久的`serialVersionUID`常量来覆盖此行为。程序员有责任确保所有具有相同`serialVersionUID`的类版本是兼容的。

Objective-C 也使用类版本，但方式完全不同。每个类都有一个`version`属性，编码器会将该属性包含在归档中。Objective-C 并不关心你的类版本是否与正在解码的版本不同。在解码时，你的类可以查询编码器，以确定用于编码归档的类版本。你可以利用此信息，以与先前版本兼容的方式解码这些值。

为了使这一点有效，你必须为每个功能上不同的类版本分配一个唯一的版本号，并且在编码任何归档之前，必须设置类的`version`属性。默认情况下，每个类的版本都是`0`。清单 12-11 展示了来自清单 12-5 的`ScheduledEvent`类的实现，已重写以提供向后兼容性。为清晰起见，已移除键归档支持。

**清单 12-11. 顺序归档中的向后兼容性**

```
+ (void)initialize
{
    [ScheduledEvent setVersion:1]; // 第二个版本
}

- (id)initWithCoder:(NSCoder*)decoder
{
    self = [super init];
    if (self != nil) {
        startTime = [decoder decodeObject];
        if ([decoder versionForClassName:@"ScheduledEvent"] == 1) {
            endTime = [decoder decodeObject];
            timeZone = [decoder decodeObject];
        } else {
            NSTimeInterval duration;
            [decoder decodeValueOfObjCType:@encode(NSTimeInterval) at:&duration];
            endTime = [startTime addTimeInterval:duration];
            timeZone = [NSTimeZone localTimeZone];
        }
        [decoder decodeValueOfObjCType:@encode(unsigned int)
                                    at:&room.buildingNo];
        [decoder decodeValueOfObjCType:@encode(unsigned int)
                                    at:&room.roomNo];
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
    [encoder encodeObject:startTime];
    [encoder encodeObject:endTime];
    [encoder encodeObject:timeZone];
    [encoder encodeValueOfObjCType:@encode(unsigned int) at:&room.buildingNo];
    [encoder encodeValueOfObjCType:@encode(unsigned int) at:&room.roomNo];
}
```

`+initialize`类方法用于在创建该类的任何实例之前设置类的版本。更多关于`+initialize`方法的信息，请参见第 21 章。在解码时，对象会查询编码器以发现用于创建归档的类的版本号，并相应调整其解码逻辑。

每当你的类的编码方式发生变化时，你必须建立一个新的类版本，并更新解码器以处理你想要支持的所有早期格式。类版本控制无法提供前向兼容性。那将需要一台时间机器。

## 类替换

有时，对应用程序的更改不仅仅涉及添加或重新定义成员变量。重构应用程序可能涉及重命名或完全废弃某些类。当尝试解码由应用程序早期版本编写的归档时，这会成为一个问题，因为归档中记录的类已不再存在。这个问题通常可以通过在编码或解码期间使用类替换来解决。

### 解码期间的类替换

假设我们的日程安排应用程序已被重构，完全消除了`ScheduledEvent`类。它已被一个`AbstractEvent`类及其子类`MeetingEvent`、`ProjectEvent`和`HolidayEvent`取代。任何解码包含`ScheduledEvent`对象的归档的尝试都将失败，因为解码器无法找到要创建的`ScheduledEvent`类。有三种解决方案，而且所有方案都涉及创建一个仅用于提供向后兼容性的替身`ScheduledEvent`类。

第一种解决方案是实现一个外壳（shell）`ScheduledEvent`类，其中包含一个传统的`-initWithCoder:`方法。它还会像前面“重复对象”部分描述的那样，覆盖`-awakeAfterUsingCoder:`方法。在后一个方法中，将创建一个等价对象来替换原始对象。

一种更直接的方法借鉴了类簇（class clusters）的思想——参见第 22 章——在`-initWithCoder:`方法中直接执行对象替换，如清单 12-12 所示。当编码器尝试初始化一个新创建的`ScheduledEvent`对象时，构造函数会销毁该临时对象，并创建一个具有正确类的新对象来替代。

**清单 12-12. 在解码期间替换一个类**

```
@interface ScheduledEvent : NSObject <NSCoding>
@end

@implementation ScheduledEvent

- (id)initWithCoder:(NSCoder*)decoder
{
    self = [super init];
    if (self != nil) {
        // 读取已废弃的 ScheduledEvent 的属性
        NSDate *startTime = [decoder decodeObjectForKey:@"Start"];
        NSTimeInterval duration = [decoder decodeDoubleForKey:@"Duration"];
        RoomIdentifier room;
        room.buildingNo = [decoder decodeInt32ForKey:@"Room.building"];
        room.roomNo = [decoder decodeInt32ForKey:@"Room.number"];

        // 用等价的 MeetingEvent 对象替换它
        id replacement = [MeetingEvent new];
        [replacement setStartTime:startTime];
        [replacement setEndTime:[startTime addTimeInterval:duration]];
        [replacement setRoom:room];
        self = replacement;
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
    [NSException raise:NSInvalidArchiveOperationException
                format:@"ScheduledEvent obsolete"];
}

@end
```

解归档器（unarchiver）的委托对象也可以在不要求对象本身协作的情况下，执行解码时的对象替换。当对象被解码时，会向解归档器的委托对象发送一条`-unarchiver:didDecodeObject:`消息。委托可以选择返回一个与原始对象不同的对象，从而替换它。解归档器必须通过在解码任何对象之前设置其`delegate`属性来进行自定义。使用清单 12-2 中的代码作为创建自定义解码器的模板。第 17 章详细解释了委托对象。

> **注意：** 如果对象包含循环引用，解码期间的对象替换将无法可靠工作。循环引用会导致对象被递归地构造。返回给嵌套构造函数的对象引用将是部分初始化的对象，此时它尚未完成执行`-initWithCoder:`或收到`-replacementObjectForCoder:`消息。即使第一个对象已经替换了自身，第二个创建的对象仍将引用原始对象。

### 编码期间的类替换

在其他情况下，一个类可能不希望归档自身。该类可能是某个类簇的私有子类——关于类簇的更多信息，请参见第 22 章。或者，为了与早期设计实现前向兼容性，它可能希望将其自身归档为另一个不同的类。无论出于何种原因，一个类可以选择在编码期间“假装”是另一个类，或者提供一个完全不同的对象来编码到它的位置。有三种方法可以实现编码时替换。


