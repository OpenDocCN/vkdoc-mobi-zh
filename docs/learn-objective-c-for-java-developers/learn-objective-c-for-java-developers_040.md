# 第 12 章 ■ 序列化

### 清单 12-3. 使用归档实现文档类

```objc
@interface SimpleDocument : NSDocument {

id dataModel;

}

@end

@implementation SimpleDocument

- (NSData*)dataOfType:(NSString*)typeName error:(NSError**)outError

{

return [NSKeyedArchiver archivedDataWithRootObject:dataModel];

}

- (BOOL)readFromData:(NSData*)data

ofType:(NSString*)typeName

error:(NSError**)outError

{

dataModel = [NSKeyedUnarchiver unarchiveObjectWithData:data];

return (dataModel!=nil);

}

@end
```

## 为你的类添加键控归档支持

与 Java 类似，类默认是不可归档的。你的类必须遵循`NSCoding`协议，并实现`-initWithCoder:`和`-encodeWithCoder:`方法。清单 12-4 展示了一个示例。

Java 等价代码被省略，因为在简单情况下无需编写任何 Java 代码。

### 清单 12-4. 支持键控归档的类

```objc
typedef struct {

unsigned int buildingNo;

unsigned int roomNo;

} RoomIdentifier;

@interface ScheduledEvent : NSObject <NSCoding> {

@private

NSDate *startTime;

NSTimeInterval duration;

RoomIdentifier room;

}

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

