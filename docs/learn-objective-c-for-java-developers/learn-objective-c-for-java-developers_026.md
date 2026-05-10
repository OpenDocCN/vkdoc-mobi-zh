# 第八章

## 字符串和原始值

Java 和 Objective-C 对原始值的处理方法非常相似。两者都对原始标量值（即数字）和数组提供了直接的语言支持。两者都提供了一组“包装器”对象，当原始值需要被当作对象处理时，它们可以封装这些原始值。两者都提供了声明字符串对象字面量的语法。

本章介绍了用于在对象和字符串类中“包装”原始值的类。本章的其余部分将重点介绍字符串的转换和格式化。

### 包装标量原始值

与 Java 类似，Objective-C 变量可以大致分为原始标量值和对象引用值。要将整数值传递给一个期望对象的方法，您必须将原始值“包装”或“装箱”到一个封装了原始值的对象中。与 Java 一样，Objective-C 提供了一组专门用于包装原始 C 变量类型的类。标量值类型已在第二章中列出。表 8-1 列出了这些类型的包装器对象。

**表 8-1.** 原始标量类型包装器类

| Java 类型 | Java | Objective-C |
|-----------|------|-------------|
| `boolean` | `new Boolean(x)` | `[NSNumber numberWithBool:x]` |
| `byte` | `new Byte(x)` | `[NSNumber numberWithChar:x]` |
| `char` | `new Character(x)` | `[NSNumber numberWithUnsignedShort:x]` |
| `short` | `new Short(x)` | `[NSNumber numberWithShort:x]` |
| `int` | `new Integer(x)` | `[NSNumber numberWithInteger:x]` |
| `long` | `new Long(x)` | `[NSNumber numberWithLongLong:x]` |
| `float` | `new Float(x)` | `[NSNumber numberWithFloat:x]` |
| `double` | `new Double(x)` | `[NSNumber numberWithDouble:x]` |

我相信您会注意到表 8-1 中的规律。Java 为每种标量类型提供了单独的类，而 Objective-C 则使用单一的 `NSNumber` 类来封装所有数字类型。在内部，`NSNumber` 以其原始形式存储原始值的副本。

为简洁起见，表 8-1 中省略了用于无符号整数的并行构造方法集，但它们都遵循相同的形式：`-[NSNumber numberWithUnsignedChar:]`、`-[NSNumber numberWithUnsignedShort:]`，等等。

#### 标量类型转换



