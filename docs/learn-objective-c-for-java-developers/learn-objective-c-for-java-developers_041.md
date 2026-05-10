# 第 12 章 ■ 序列化

```objc
@implementation ScheduledEvent

- (id)initWithCoder:(NSCoder*)decoder

{

self = [super init];

if (self != nil) {
```



```objectivec
startTime = [decoder decodeObjectForKey:@"Start"];
duration = [decoder decodeDoubleForKey:@"Duration"];
room.buildingNo = [decoder decodeInt32ForKey:@"Room.building"];
room.roomNo = [decoder decodeInt32ForKey:@"Room.number"];
}

return self;

}

- (void)encodeWithCoder:(NSCoder*)encoder

{

[encoder encodeObject:startTime forKey:@"Start"];

[encoder encodeDouble:duration forKey:@"Duration"];

[encoder encodeInt32:room.buildingNo forKey:@"Room.building"];

[encoder encodeInt32:room.roomNo forKey:@"Room.number"];

}

@end
```

现在，`ScheduledEvent` 对象可以使用 `NSKeyedArchiver` 类进行归档。清单 12-4 中的代码仅支持键控归档，因此该对象仍然不能用于顺序归档或分布式对象。创建可归档类的基本步骤如下：

- 遵循 `NSCoding` 协议，或继承一个遵循 `NSCoding` 协议的类。
- 实现 `-initWithCoder:` 方法，使用解码器中的数据初始化新对象。如果该类继承自 `NSCoding`，则该方法应以 `self = [super initWithCoder:decoder]` 开头。
- 实现 `-encodeWithCoder:` 方法，对对象进行编码。如果该类继承自 `NSCoding`，则该方法应以 `[super encodeWithCoder:coder]` 开头。
- 通过向编码器对象发送适当的 `-encode…` 或 `-decode…` 消息，对所有持久化成员变量进行编码或解码。复杂的变量（如结构体）必须解构为其原始基本值。编码方法列于表 12-2 中。

用于键控归档的 `NSCoding` 方法仅应发送 `-encode…:forKey:` 和 `-decode…ForKey:` 消息。用于顺序归档或分布式对象的类仅应发送 `-encode…:` 和 `-decode…` 消息。如何使一个类同时支持这两种方式将在稍后描述。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第十二章 ■ 序列化

### 表 12-2. 编码方法

| Objective-C 类型 | 键控编码器 | 顺序编码器 |
|-----------------|---------------|--------------------|
| `id` | `encodeObject:forKey:` | `encodeObject:` |
| `NSInteger` | `encodeInteger:forKey:` | `int` |
| `long long int` | `encodeInt32:forKey:` | - |
| `BOOL` | `encodeBool:forKey:` | - |
| 字节数组 | `encodeBytes:length:forKey:` | `encodeBytes:length:` |
| `double` | `encodeDouble:forKey:` | - |
| `float` | `encodeFloat:forKey:` | - |
| `NSPoint` | `encodePoint:forKey:` | `encodePoint:` |
| `NSRect` | `encodeRect:forKey:` | `encodeRect:` |
| `NSSize` | `encodeSize:forKey:` | `encodeSize:` |
| 任何 C 类型 | - | `encodeValueOfObjCType:at:` |

你为键选择的值完全是任意的。你可以选择使用任何你喜欢的键，只需保证这些键在对象的范围内保持一致且唯一即可。

与典型的 Java 序列化不同，你可以完全控制哪些值被编码，以及它们在解码时如何被解释。你可以自由选择你的对象在数据流中的表示方式。例如，假设有一个出版应用程序，包含一个墨水颜色对象。该对象的 `-encodeWithCoder:` 方法可以简单地编码墨水颜色的所有属性（青、品红、黄、黑等的量），也可以仅编码颜色的潘通色号。当对象被解码时，它将使用颜色名称来用等价的颜色值初始化自身。像这样的技术可以使你对象的编码版本更具可移植性和持久性。

> **注意：** 在 `-initWithCoder:` 方法的作用域内，避免向由 `-decodeObject...` 返回的对象指针发送消息。循环引用可能导致 `-decodeObject…` 返回的对象指针引用一个部分初始化的对象。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第十二章 ■ 序列化

两个 `NSCoding` 方法都接收一个 `NSCoder` 对象。这个对象可能是同一个，但通常不同。在键控归档的情况下，`-initWithCoder:` 会接收到一个 `NSKeyedUnarchiver` 对象，而 `-encodeWithCoder:` 会接收到一个 `NSKeyedArchiver` 对象。`NSKeyedArchiver` 仅实现了 `-encode…:forKey:` 方法，任何向其发送 `-decode…` 消息的尝试都会引发异常。`NSKeyedUnarchiver` 仅实现了对应的 `-decode…ForKey:` 方法。



### 为类添加顺序归档支持

添加顺序归档支持（包含分布式对象）与添加键值归档支持几乎完全相同，但有两个例外：

- 顺序归档使用表 12-2 中的顺序编码方法。
- 在 `-encodeWithCoder:` 中编码的属性的顺序和类型，必须与在 `-initWithCoder:` 中解码时的顺序和类型完全一致。与键值编码不同，你不能忽略某些值，也不能以与编码时不同的顺序进行解码。

你会注意到顺序编码方法的数量要少得多。最明显的是缺少用于编码原始 C 类型的方法。这是因为顺序编码器提供了通用的 `-encodeValueOfObjCType:at:` 方法。该方法接受一个编码后的 C 变量类型字符串（使用 `@encode()` 指令生成），以及一个变量的地址。要编码列表 12-4 中的 `duration` 属性，你可以这样写：`[encoder encodeValueOfObjCType:@encode(NSTimeInterval) at:&duration]`。虽然理论上可以传递任何 C 类型的类型字符串（例如，对于像 `@encode(RoomIdentifier)` 这样的任意结构体），但编码器通常只实现语言定义的原始类型。在上一个例子中，`NSTimeInterval` 与 `double` 是同义词，因此 `@encode(NSTimeInterval)` 和 `@encode(double)` 可以互换。你可以使用 `-encodeArrayOfObjCType:count:at:` 方法来编码一个原始值构成的完整数组。

### 同时支持键值归档和顺序归档

但如果你希望你的类同时支持键值归档、顺序归档以及分布式对象呢？这很简单：向编码器发送 `-allowsKeyedCoding` 消息来判断它是否支持键值归档，然后使用相应的编码器方法即可。列表 12-5 展示了一个 `ScheduledEvent` 类的扩展实现，它同时支持键值归档和顺序归档。

**列表 12-5.** 支持键值和顺序归档的类

```
- (id)initWithCoder:(NSCoder*)decoder
{
    self = [super init];
    if (self != nil) {
        if ([decoder allowsKeyedCoding]) {
            startTime = [decoder decodeObjectForKey:@"Start"];
            duration = [decoder decodeDoubleForKey:@"Duration"];
            room.buildingNo = [decoder decodeInt32ForKey:@"Room.building"];
            room.roomNo = [decoder decodeInt32ForKey:@"Room.number"];
        } else {
            startTime = [decoder decodeObject];
            [decoder decodeValueOfObjCType:@encode(NSTimeInterval) at:&duration];
            [decoder decodeValueOfObjCType:@encode(unsigned int) at:&room.buildingNo];
            [decoder decodeValueOfObjCType:@encode(unsigned int) at:&room.roomNo];
        }
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
    if ([encoder allowsKeyedCoding]) {
        [encoder encodeObject:startTime forKey:@"Start"];
        [encoder encodeDouble:duration forKey:@"Duration"];
        [encoder encodeInt32:room.buildingNo forKey:@"Room.building"];
        [encoder encodeInt32:room.roomNo forKey:@"Room.number"];
    } else {
        [encoder encodeObject:startTime];
        [encoder encodeValueOfObjCType:@encode(NSTimeInterval) at:&duration];
        [encoder encodeValueOfObjCType:@encode(unsigned int) at:&room.buildingNo];
        [encoder encodeValueOfObjCType:@encode(unsigned int) at:&room.roomNo];
    }
}
```

顺序编码器不实现任何 `…forKey:` 方法，尝试调用这些方法会引发异常。同样，键值编码器也不实现任何顺序方法，因此请确保你知道自己正在使用的是哪种编码器。

如果你想要在程序层面限制对象的归档支持，你可以在 `-encodeWithCoder:` 方法中有条件地引发异常。列表 12-6 中的类同时支持键值和顺序归档，但不允许通过分布式对象连接进行复制。这相当于覆盖 Java 对象的 `writeObject(ObjectOutputStream)` 方法并抛出 `NotSerializableException` 异常。

**列表 12-6.** 在程序层面限制归档支持

```
- (void)encodeWithCoder:(NSCoder*)encoder
{
    if ([encoder isKindOfClass:[NSPortCoder class]])
```


```objectivec
[NSException raise:NSInvalidArchiveOperationException
format:@"%@ not distributable",[self className]]; if ([encoder allowsKeyedCoding]) {
// Keyed encoding...
} else {
// Sequential encoding...
}
}
```

### 归档的复杂性

与软件工程中的几乎所有事物一样，简单的案例很容易处理；真正棘手的是边界条件。归档（序列化）也不例外。这里有许多令人困扰的问题需要处理。Java 和 Objective-C 的处理方式大多相似。这些问题包括瞬态值、保存数据与不同类版本之间的兼容性、共享对象、对象图之外的对象以及重复对象。

#### 瞬态属性

Java 提供了 `transient` 关键字来标记不应包含在序列化数据流中的实例变量。在 Objective-C 中，解决方案是在编码和解码过程中简单地忽略该属性。列表 12-7 展示了 `ScheduledEvent` 类的修改版本，其中包含一个屏幕坐标属性，用于保存预定事件最后显示的位置。当事件对象存储在文档中时，该值无关紧要，因此它被排除在数据流之外。

**列表 12-7.** 瞬态属性

**Java**

```java
public class ScheduledEvent implements Serializable {
Date startTime;
double duration;
RoomIdentifier room;
transient java.awt.Point lastScreenPopupPosition;
…
}
```

**Objective-C**

```objectivec
@interface ScheduledEvent : NSObject <NSCoding> {
NSDate *startTime;
NSTimeInterval duration;
RoomIdentifier room;
NSPoint lastScreenPopupPosition;
}
@end

@implementation ScheduledEvent
…
- (void)encodeWithCoder:(NSCoder*)encoder
{
[encoder encodeObject:startTime forKey:@"Start"];
[encoder encodeDouble:duration forKey:@"Duration"];
[encoder encodeInt32:room.buildingNo forKey:@"Room.building"];
[encoder encodeInt32:room.roomNo forKey:@"Room.number"];
// 不编码 lastScreenPopupPosition
}
@end
```

#### 重复对象

用于创建归档的对象称为根对象。它在一个无向对象图中形成锚点，该图包含根对象引用的所有对象，以及这些对象引用的任何对象，依此类推。对象图可能很简单，比如数组或树，但也可能形成循环和循环引用。如果编码器盲目地跟随每个对象引用，那么对单个对象的多次引用会导致该对象的数据被多次编码，而循环引用则会导致无限递归。Java 的 `java.io.ObjectOutputStream` 和 Objective-C 的 `NSCoder` 类几乎以相同的方式解决了这个问题。当一个对象首次通过 `-encodeObject:` 消息发送给编码器时，编码器会递归地向该对象发送 `-encodeWithCoder:` 消息，以在流中编码其数据。编码器还会记住该对象实例。之后所有引用同一对象的 `-encodeObject:` 消息，都只是在数据流中插入一个指向原始对象的引用。解码时，所有 `-decodeObject` 消息都会返回指向单一解归档对象实例的指针。你无需做任何操作即可获得此行为；了解其工作原理总是有益的。

解归档可能会无意中创建重复对象。如果编码对象引用了共享池中的对象，就可能发生这种情况。解码器会为数据流中的每个唯一对象创建新实例。这会导致你的应用程序中出现共享对象的副本。有几种方法可以处理这个问题。一种是使用如前文潘通色彩示例中给出的编码方案；对对象进行某种符号化表示编码，然后从公共资源中获取其实际内容。另一种解决方案是重写 `- (id)awakeAfterUsingCoder:(NSCoder*)decoder` 方法。该消息在对象被解码后发送给它。该方法返回的对象标识符将替换已解码的对象。默认实现返回 `self`（即不替换），但如果你重写了它，则可以返回任何等效对象。列表 12-8 演示了一个 `Attendee` 类，它避免了创建重复的 `Attendee` 对象，并将任何新的 `Attendee` 对象添加到公共池中。

**列表 12-8.** 解码共享对象

```objectivec
@interface Attendee : NSObject <NSCoding> {
NSString *name;
NSString *uuid;
NSMutableSet *scheduledMeetings;
}
@end

@implementation Attendee
- (id)initWithCoder:(NSCoder*)decoder
{
self = [super init];
if (self != nil) {
name = [decoder decodeObjectForKey:@"Name"];
uuid = [decoder decodeObjectForKey:@"UUID"];
scheduledMeetings = [decoder decodeObjectForKey:@"Meetings"];
}
return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
[encoder encodeObject:name forKey:@"Name"];
[encoder encodeObject:uuid forKey:@"UUID"];
[encoder encodeObject:scheduledMeetings forKey:@"Meetings"];
}

- (id)awakeAfterUsingCoder:(NSCoder*)decoder
{
ScheduleAssets *assets = [ScheduleAssets sharedAssets];
Attendee *existingAttendee = [assets attendeeWithUUID:uuid];
if (existingAttendee==nil) {
// 将此参与者添加到参与者池中
[assets addAttendee:self];
} else {
// 用已存在的参与者替换此参与者
self = existingAttendee;
}
return self;
}
@end
```

#### 限制对象图

有时归档一个根对象会归档远超你意图的内容。归档根对象通常会归档它所引用的所有对象。在我们假设的日程安排应用程序中，一个 `ProjectMeeting` 对象将一个会议与一个项目任务关联起来。将 `ProjectMeeting` 对象归档并通过电子邮件发送给团队成员以邀请他们参加会议会很方便。问题是，列表 12-9 中的 `ProjectMeeting` 对象包含一个对会议所关联的 `ProjectTask` 对象的引用。任务对象自然包含对其项目的引用，而项目又包含对所有项目任务、其里程碑、参与项目的团队成员、为项目安排的其他会议的引用，等等。简而言之，我们尝试向某人发送一个会议对象，结果会导致归档整个项目日程安排系统，甚至可能包括应用程序本身。

Objective-C 的解决方案是有条件地编码对象。被编码的对象图仅包含使用 `-encodeObject:` 或 `-encodeObject:forKey:` 添加到流中的无条件对象。条件对象则使用 `-encodeConditionalObject:` 或 `-encodeConditionalObject:forKey:` 进行编码（参见列表 12-9）。只有当这些对象至少被无条件编码过一次时，它们才会被包含在归档中。如果对象的所有包含关系都是条件性的，则该对象将从归档中被排除。当对象图被解码时，针对被排除对象的 `-decodeObject...` 请求将返回 `nil`。

**列表 12-9.** 编码条件对象

```objectivec
@interface ProjectMeeting : ScheduledEvent {
NSString *meetingDescription;
ProjectTask *task;
}
@end

@implementation ProjectMeeting
- (id)initWithCoder:(NSCoder*)decoder
{
self = [super initWithCoder:decoder];
if (self != nil) {
meetingDescription = [decoder decodeObjectForKey:@"Description"];
task = [decoder decodeObjectForKey:@"Task"];
}
return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
[super encodeWithCoder:encoder];
[encoder encodeObject:meetingDescription forKey:@"Description"];
[encoder encodeConditionalObject:task forKey:@"Task"];
}
@end
```


#### 类版本兼容性

持久化数据的问题在于它确实会持久存在。这些数据可能持续存在数天甚至数年，而你的应用程序则在不断演进。最终，你的类会尝试解码由早期版本自身编码的归档文件。有时，甚至可能需要解码由同一类的某个后续版本创建的归档文件。

Java 通过结合使用类版本和键/值编码来解决兼容性问题。Java 的类版本严格用于禁止解码可能与类不兼容的数据。Java 序列化也使用键/值编码，就像 Objective-C 的键控归档一样。但在 Java 中，键始终是实例变量的名称。Java 会自动恢复与其先前版本共有的变量，并直接忽略数据流中未编码的变量。更复杂的兼容性问题需要你实现一个自定义的 `readObjects(ObjectInputStream)` 方法。我打算跳过冗长的细节，因为这不是一本关于 Java 的书。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**注意：** 向后兼容是指较新版本的类/应用程序能够解释由较旧版本创建的数据。向前兼容是指较旧版本的类/应用程序能够（至少部分地）解释由较新版本创建的数据。

如何在 Objective-C 中提供向后或向前兼容性，取决于归档类型。

## 键控归档中的向前与向后兼容性

键控归档有一个巨大的优势：类版本之间灵活的兼容性。如列表 12-10 所示，改进后的 `ScheduledEvent` 类与列表 12-4 中的初始版本相比略有变化。它包含了一个新的时区对象，并且持续时间值已被结束时间对象所取代。

列表 12-10. 键控归档中的向前与向后兼容性

```
@interface ScheduledEvent : NSObject <NSCoding> {
@private
    NSDate *startTime;
    NSDate *endTime;
    NSTimeZone *timeZone;
    RoomIdentifier room;
}

@implementation ScheduledEvent

- (id)initWithCoder:(NSCoder*)decoder
{
    self = [super init];
    if (self != nil) {
        if ([decoder containsValueForKey:@"TimeZone"])
            timeZone = [decoder decodeObjectForKey:@"TimeZone"];
        else
            timeZone = [NSTimeZone localTimeZone];

        startTime = [decoder decodeObjectForKey:@"Start"];
        if ([decoder containsValueForKey:@"End"]) {
            endTime = [decoder decodeObjectForKey:@"End"];
        } else {
            NSTimeInterval duration = [decoder decodeDoubleForKey:@"Duration"];
            endTime = [startTime addTimeInterval:duration];
        }

        room.buildingNo = [decoder decodeInt32ForKey:@"Room.building"];
        room.roomNo = [decoder decodeInt32ForKey:@"Room.number"];
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
    [encoder encodeObject:startTime forKey:@"Start"];
    [encoder encodeObject:endTime forKey:@"End"];
    [encoder encodeObject:timeZone forKey:@"TimeZone"];
    [encoder encodeInt32:room.buildingNo forKey:@"Room.building"];
    [encoder encodeInt32:room.roomNo forKey:@"Room.number"];
    [encoder encodeDouble:[endTime timeIntervalSinceDate:startTime]
                   forKey:@"Duration"];
}

@end
```

列表 12-10 中的实现提供了与其初始版本的向前和向后兼容性。也就是说，该类的新版本可以解码由原始版本创建的归档，而原始版本也可以解码由新版本创建的归档。这是通过明智地使用归档键来实现的。

`ScheduledEvent` 的新版本有一个旧版本所没有的时区对象。新版本使用 `-containsValueForKey:` 方法来判断它正在解码的归档是否包含该键的值。如果包含，则读取该值；如果不包含，则假定该归档是由该类的早期版本创建的，并提供一个合理的默认值。旧版本的类在读取较新归档时，会忽略该值，因为其对时区一无所知。

用 `endTime` 替换持续时间变量需要做更多工作。同样，`-initWithCoder:` 使用 `[decoder containsValueForKey:@"End"]` 来判断归档是否包含 `"End"` 值。现代的归档会包含，但旧的则不会。新类假设缺少 `"End"` 值意味着它正在读取旧版本的归档；它使用旧版本写入的 `"Duration"` 值来构造一个等价的 `endTime` 对象。

为了提供向前兼容性，它将 `endTime` 值存档了两次。一次作为 `NSDate` 对象，另一次作为与该类原始持续时间值兼容的 `NSTimeInterval`。另一种保持兼容性的方法是完全不编码 `endTime` 对象，并继续编码与旧版本兼容的 `"Duration"` 值。当新属性在逻辑上等同于旧属性，并且易于转换时，这将是首选的解决方案。

以下是在键控归档中维护向后以及潜在的向前兼容性的一些技巧：

-   测试该类后续版本中添加的键是否存在。键的缺失表明归档是由早期版本创建的。
-   如果某个值的类型发生了变化，请使用新的键对其进行编码。
-   整数和浮点数解码方法会执行一些适度的类型转换。所有的整数编码和解码方法都是可互换的，浮点数方法也是如此。因此，你可以将一个数字编码为 32 位整数，然后将其解码为 64 位整数，反之亦然。
-   用一些合理的值来初始化缺失的键。
-   为了向前兼容，请继续写入该类早期版本所期望的键和值，或者将较新的值转换为与旧类兼容的值。
-   考虑插入一个 `"version"` 值，或任何其他类型的提示，以帮助未来的类确定如何解释归档值。语句 `[encoder encodeBool:YES forKey:@"isTimeZoneSavvy"]` 将通知未来的解码器，此归档是由一个理解时区的类版本创建的。

## 顺序归档中的向后兼容性

列表 12-9 中的代码通过有条件地编码其对 `ProjectTask` 对象的引用来解决这个问题。如果你归档一个单独的 `ProjectMeeting` 对象，`ProjectTask` 对象永远不会被无条件地编码，从而被省略。当解档时，语句 `[decoder decodeObjectForKey:@"Task"]` 返回 `nil`。结果对象将不会连接到项目调度数据模型，但足以用于邀请某人参加会议。

当调度系统服务器归档整个项目数据模型时，它将无条件地编码项目、其任务、里程碑和团队成员对象。当它处理到 `ProjectMeeting` 对象时，条件对象引用会被正常编码，因为 `ProjectTask` 对象已经在图中的其他位置被无条件地编码了。当第二天项目数据模型被解档时，`ProjectMeeting` 对其 `ProjectTask` 的引用会被恢复。

通常，当对象指针是 `__weak` 引用，或者该对象是委托、父对象、所有者或容器时，有条件地编码对象是合适的。



