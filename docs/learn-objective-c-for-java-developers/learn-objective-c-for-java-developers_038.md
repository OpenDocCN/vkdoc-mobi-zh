# Java 与 Objective-C 中的对象序列化

对象序列化是 Java 的定义性特征之一，主要由 Java 运行时实现。秉承其极简主义特性，对象序列化并非 Objective-C 语言的原生组成部分。

对象归档（序列化）是通过一组实现序列化过程的类，以及一个对象为了能被归档（序列化）而必须遵循的协议（接口）来完成的。

Java 最大的优势在于序列化是语言的一部分。对象的所有成员变量都会被自动序列化。而在 Objective-C 中，除非程序员提供相应代码，否则对象的任何部分都不会被序列化。除此之外，你为准备对象进行序列化、执行序列化和反序列化所需采取的步骤却惊人地相似。Java 和 Objective-C 都允许你自定义序列化过程，并且都提供了处理向前兼容和向后兼容的机制。

本章将介绍 Objective-C 归档（序列化）和 Objective-C 序列化——请注意不要与 Java 序列化混淆。本章会解释 Objective-C 提供的不同类型的归档，以及让你的类可归档所需采取的步骤。同时还会涵盖常见的归档问题及其解决方法。最后，将简要介绍对 Objective-C 序列化和 XML 文档（序列化的其他形式）的支持，以及如何创建对象的简单内存副本。

### 归档

你在 Java 中认为的序列化，在 Objective-C 中被称为归档：一个对象图被编码成非人类可读的二进制数据流。这种与架构无关的数据之后可以被解码，以实例化一个等价的对象图。Objective-C 所谓的序列化略有不同，将在本章后面部分介绍。

在 Java 中，主要负责序列化对象的类是 `java.io.ObjectOutputStream` 和 `java.io.ObjectInputStream`。它们获取对象引用和原始值，并将其“扁平化”到连续的 `java.io.OutputStream` 中，或者从 `java.io.InputStream` 中读取序列化数据并将其还原为对象。要使对象能够包含在流中，它必须实现 `java.io.Serializable` 接口。Java 运行时使用内省机制来自动编码对象的实例变量。

你可以通过使用 Java 的 `transient` 关键字或自定义序列化过程来影响这一行为。

在 Objective-C 中，实现序列化的类是 `NSCoder` 的子类。

从概念上讲，`NSCoder` 同时实现了 `ObjectOutputStream` 和 `ObjectInputStream` 的功能。`NSCoder` 基类实现了用于编码和解码对象的抽象方法，但 `NSCoder` 的子类可以选择仅实现其一半的功能。与 Java 类似，支持归档的 Objective-C 类必须遵循 `NSCoding` 协议（接口）。与 Java 不同的是，`NSCoding` 并非空协议；它声明了两个必须由类实现的方法：`-initWithCoder:` 和 `-encodeWithCoder:`。`-encodeWithCoder:` 消息用于归档接收者。`-initWithCoder:` 是一个备用的 `-init` 方法，用于初始化在解码过程中创建的新对象。

#### 归档类型

Objective-C 实际上提供了三种不同类型的归档，如表 12-1 所示。

**表 12-1. 归档类型**

| 类型                 | NSCoder 类                        | 描述                                                         |
| -------------------- | -------------------------------- | ------------------------------------------------------------ |
| 键值归档             | `NSKeyedArchiver`, `NSKeyedUnarchiver` | 使用键/值对来归档对象属性。这是归档文档数据或其他持久化对象的首选方法。 |
| 顺序归档             | `NSArchiver`, `NSUnarchiver`     | 通过按特定顺序写入属性值来归档对象。作为持久化存储格式，其使用已被弃用。 |
| 分布式对象           | `NSPortCoder`                    | 使用顺序编码来归档对象，用于与其他线程、进程和远程系统交换。 |

键值归档会将每个属性值与程序员分配的“键”字符串关联起来。



Java 序列化与之类似，但键始终是成员变量的名称。这是一种特别灵活且健壮的格式，能够容忍类定义的变更。通过使用键，属性在数据流中的编码顺序无关紧要。这允许你添加新属性或重新排序现有属性，而不会破坏与使用旧版本类创建的存档的兼容性。你还可以提供前向兼容性，允许旧版本的应用程序解码由新版本写入的存档。键控归档会产生更多的序列化数据，但最终更加灵活和稳定。因此，它是文档数据或任何旨在存储在持久化介质上以便后续重构的对象集的推荐归档格式。

顺序归档会以预定顺序将未修饰的值编码到数据流中。不使用属性名称或键来标识这些值，也不包含任何类型信息。要重建对象，必须按相同的顺序解码这些值。虽然快速且紧凑，但数据格式是“脆弱的”。任何对值的顺序、数量或类型的更改都会使其与不同版本创建的归档数据流不兼容。Objective-C 通过使用类版本来解决这个问题。较新版本的类可以识别并吸收旧版本创建的归档数据。虽然提供了向后兼容性，但它不提供前向兼容性，而且解决方案往往很笨拙。由于这些原因，不建议将顺序归档用于文档数据或其他持久化对象。然而，这并不是说顺序归档没有被使用。

分布式对象使用顺序归档（加上一些额外的技巧）来与其他进程快速高效地交换对象。这些进程可能是同一进程中的另一个线程、同一系统上的另一个进程，或者位于地球另一端的一个远程系统。编码和解码技术保持不变，只是数据传输方式发生了变化。因为分布式对象使用了顺序归档，所以它也具有所有相同的缺点。然而，进程之间的版本差异通常是有限的，或者可以被严格控制。例如，客户端应用程序可能会启动一个辅助进程并使用分布式对象与之通信。该辅助可执行文件是应用程序资源包的一部分，因此保证包含客户端正在使用的类的相同版本——这使得数据兼容性变得无关紧要。

关于 Objective-C 归档的文档会不厌其烦地重复，顺序归档已被弃用，你应该改用键控归档。这对于打算存储在文档文件、偏好设置或其他持久化位置的数据模型对象来说是正确的。然而，分布式对象使用了顺序归档。要在分布式环境中使用你的对象，你的类必须实现顺序归档，并且理解分布式对象需要牢固掌握归档技术。本章将解释如何实现这两种归档。区分分布式对象与普通顺序归档的特性将在第 13 章中描述。

#### 归档编码器

使用归档编码器类来编码和解码对象与 Java 中的做法并没有太大不同。代码清单 12-1 展示了在 Java 和 Objective-C 中如何对一个对象进行归档（序列化）和解归档。

**代码清单 12-1.** 归档和解归档一个对象

**Java**

```
Object something = …
ByteArrayOutputStream outStream = null;
ObjectOutputStream objectEncoder = null;
try {
    outStream = new ByteArrayOutputStream();
    objectEncoder = new ObjectOutputStream(outStream);
    objectEncoder.writeObject(something);
    objectEncoder.close();
}
catch (IOException ioException) {
    ioException.printStackTrace();
}
byte[] bytes = outStream.toByteArray();
…
```



```java
ByteArrayInputStream inStream = null;

ObjectInputStream objectDecoder = null;

try {

inStream = new ByteArrayInputStream(bytes);

objectDecoder = new ObjectInputStream(inStream);

something = objectDecoder.readObject();

objectDecoder.close();

}

catch (Exception exception) {

exception.printStackTrace();

}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

