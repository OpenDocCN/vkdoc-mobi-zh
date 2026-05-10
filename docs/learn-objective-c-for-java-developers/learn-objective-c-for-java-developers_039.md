# 第 12 章 ■ 序列化

## **Objective-C**

```objc
id something = …;

NSData *bytes = [NSKeyedArchiver archivedDataWithRootObject:something];

…

something = [NSKeyedUnarchiver unarchiveObjectWithData:bytes];
```

清单 12-1 中的 Objective-C 代码尤为简洁，因为`NSKeyedArchiver`和`NSKeyedUnarchiver`提供了便捷方法，可以创建临时编码器对象，对单个对象进行编码或解码，并返回结果。此外还有一对方法可以直接将数据写入或读取到文件。清单 12-2 中的 Objective-C 代码更接近清单 12-1 中的 Java 代码。如果你需要在编码或解码任何对象之前自定义编码器，或者需要编码多个根对象，则应使用此形式。

### 清单 12-2. 归档和解档多个对象

```objc
id something = …;

id somethingElse = …;

NSKeyedArchiver *archiver;

NSMutableData *bytes = [NSMutableData data];

archiver = [[NSKeyedArchiver alloc] initForWritingWithMutableData:bytes];

// 在此处自定义 |archiver|...

[archiver encodeObject:something forKey:@"Something"];

[archiver encodeObject:somethingElse forKey:@"Alternate"];

[archiver finishEncoding];

// |bytes| 现在包含编码后的对象流

…

NSKeyedUnarchiver *unarchiver;

unarchiver = [[NSKeyedUnarchiver alloc] initForReadingWithData:bytes];

// 在此处自定义 |unarchiver|...

something = [unarchiver decodeObjectForKey:@"Something"];

somethingElse = [unarchiver decodeObjectForKey:@"Alternate"];

[unarchiver finishDecoding];
```

#### 归档与文档

`NSDocument`类设计为与归档紧密配合，为你的应用实现一个简单且无缝的文档存储方案。基本概念是，你的文档数据模型将包含一个可归档对象的对象图。为了将文档保存到文件中，这些对象会被归档成一个`NSData`对象，然后写入磁盘。若要重新打开文档，文档的数据文件会被读入一个新的`NSData`对象，然后解档以重新创建文档的数据模型。

清单 12-3 演示了`NSDocument`对象的最小化实现，它能够将内容读写到文档文件中。这一小段“胶水”代码就是实现 Cocoa 应用中所有标准文档命令（打开…、保存、另存为…、还原）所需的全部内容。

[www.it-ebooks.info](http://www.it-ebooks.info/)

