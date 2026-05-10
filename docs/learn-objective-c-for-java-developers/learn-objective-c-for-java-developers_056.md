# 第 3 部分

## 编程模式

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 第 16 章

#### 集合模式

组织对象集合是日常编程的基本组成部分。类框架提供了多个类，用于将对象组织成数组、字典（映射）和集合。数组中的对象具有特定的顺序，通过数字索引来寻址。字典（映射）将对象组织成无序的键值对，每对由一个唯一的键对象和一个值对象组成。键对象用于标识和寻址值对象。最后，集合是无定形的集合，既无序也不可寻址；一个对象要么在集合中，要么不在。Cocoa 框架不提供任何树、链表或栈集合。

Objective-C 中的集合模式会给 Java 程序员带来一系列挑战。最大的挑战是一种虚假的熟悉感——正如法语所说的“假朋友”。集合类在很多方面都与 Java 相似，以至于很容易忘记那些细微的差别：集合的基类是不可变的，字典（映射）中的键总是被复制的，枚举期间无法修改集合，等等。其中许多行为在常规文档中只是脚注而已。本章将强调这些差异，以便你能敏锐地意识到它们。

有些差异是福音，大多数只需对你的编程习惯稍作改变，而少数则可能深刻影响你的设计。

本章将解释表 16-1 中列出的集合类。它将描述 Objective-C 集合与 Java 对应集合的异同、相等性和哈希约定，以及如何对集合进行枚举、排序和过滤。后续部分将涵盖枚举的并发性和线程安全考虑。

***表 16-1.** Java 与 Objective-C 集合类*

| Java | Objective-C |
|------|-------------|
| `ArrayList` | `NSArray`、`NSMutableArray`、`NSPointerArray` |
| `HashMap` | `NSDictionary`、`NSMutableDictionary`、`NSMapTable` |
| `HashSet` | `NSSet`、`NSMutableSet`、`NSHashTable`、`NSCountedSet` |
| `BitSet` | `NSIndexSet` |

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 不可变集合



在 Java 中，所有集合类都是可变的。虽然可以通过 `java.util.Collections.unmodifiableCollection(Collection)` 这类特殊方法创建不可变集合，但这并不常见。大多数情况下，你设计代码时都默认所有集合是可变的，并特别留意集合何时以引用形式传递给其他方法。

大多数 Objective-C 集合类遵循**不可变基类、可变子类**的设计模式。传统集合类（`NSArray`、`NSDictionary`、`NSSet` 和 `NSIndexSet`）的基类完全是不可变的。它们缺少任何能修改集合的方法。当一个方法接收或返回这些类时，就隐含着不可变的含义——这消除了 Java 程序员常需要处理的大多数引用传递问题。你可能好奇不可变集合类到底有多大用处，但它们其实非常实用。它们采用了与 `java.lang.String` 对象相同的许多编程模式。

要交互式地构建或操作集合，必须创建可变子类之一：`NSMutableArray`、`NSMutableDictionary`、`NSMutableSet` 或 `NSMutableIndexSet`。这些子类定义了所有用于修改集合内容的方法。作为子类，你可以将任何可变集合对象作为不可变类型传递。不过，这样做时需要多加注意。在 Objective-C 中，接收不可变集合的一方很可能认为它确实是不可变的，而在 Java 中则会正确认为它是可变的。如果接收者保留了对原始对象的引用，当它的“不可变”集合被随意修改时，其行为可能会变得异常。只要没有其他代码修改该集合，或者接收方明白它实际上可能是可变集合，你就可以安全地将可变集合作为不可变集合传递。否则，请使用轻量级集合复制构造器之一，将集合转换为不可变集合。

为了让不可变类更实用且易于使用，Objective-C 提供了大量构造器和方法来创建并返回不可变集合。这些便捷构造器可以轻松创建其他集合的不可变副本，或通过创建新集合来对不可变集合进行单一修改。不可变集合的构造器列于表 16-2 中。

**表 16-2.** 不可变集合构造器

| 方法 | 描述 |
|--------|-------------|
| `+[NSArray arrayWithArray:]` | 创建另一个数组的不可变浅拷贝。 |
| `-[[NSArray alloc] initWithArray:copyItems:]` | 若 `copyItems` 参数为 `NO`，则与 `[NSArray arrayWithArray:]` 相同。若 `copyItems` 为 `YES`，则向集合中的每个对象发送 `-copyWithZone:` 消息来进行深拷贝。 |
| `+[NSArray arrayWithObject:]` | 创建包含一个对象的不可变数组。 |
| `+[NSArray arrayWithObjects:]` | 创建对象的不可变数组。对象以可变参数列表形式传递，并以 `nil` 值结尾。 |
| `+[NSArray arrayWithObjects:count:]` | 从 C 语言的对象指针数组创建不可变数组。参数为数组中第一个元素的地址和元素数量。 |
| `-[NSArray arrayByAddingObject:]` | 创建一个新的不可变数组，它是接收者数组的浅拷贝加上一个额外对象。 |
| `-[NSArray arrayByAddingObjectsFromArray:]` | 通过将接收者集合与参数中的对象连接起来，创建一个新的不可变数组。 |
| `-[NSArray subarrayWithRange:]` | 创建一个新的不可变数组，其中包含接收者数组子集的浅拷贝。 |
| `-[NSArray filteredArrayUsingPredicate:]` | 返回一个不可变数组，其中包含接收者数组中匹配谓词表达式的对象。 |
| `-[NSArray sortedArrayUsing…:]` | 四种不同方法之一，用于创建一个新的不可变数组，其中包含接收者数组排序后的内容。 |
| `+[NSDictionary dictionaryWithDictionary:]` | （原文描述继续，但此处内容已结束。） |



