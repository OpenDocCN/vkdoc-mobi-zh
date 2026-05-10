# `removeObjectsInRange`:

`removeObjectsInArray`：

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 16 章 ■ 集合模式

`set(int,Object)`

`replaceObjectAtIndex:withObject:`

`replacePointerAtIndex:`

`withPointer:`

`replaceObjectsAtIndexes:withObjects:`

`replaceObjectsInRange:`

`withObjectsFromArray:range:`

`replaceObjectsInRange:`

`withObjectsFromArray:`

`exchangeObjectAtIndex:`

`withObjectAtIndex:`

`setArray:`

`trimToSize()`

`compact`

表 16-3 和 16-4 中的一些方法并非与 Java 方法一一对应，尽管大部分都是。例如，`toArray()`返回一个数组对象，而`-getObjects:`则用集合的值填充一个 C 数组。像`arrayList.isEmpty()`这样的简单表达式很容易被替换为`[array count]==0`。大多数方法都是不言自明的。

#### `NSArray`、`NSMutableArray`

除了不允许使用`nil`（null）值之外，`NSMutableArray`可能与你所能找到的`java.lang.ArrayList`非常接近。正如你从前面的表格中看到的，这里有更多便捷方法，因此在你编写自己的代码（例如交换集合中的两个元素）之前，先查找一下数组方法。

`NSArray`区分了两个相等对象（`-indexOfObject:`）和两个相同对象（`-indexOfObjectIdenticalTo:`）之间的区别。后者比较对象指针是否相等，而前者比较对象的值是否相等。请参阅本章后面的“集合相等契约”部分。

许多方法，如`-removeObjectsAtIndexes:`，操作由`NSIndexSet`对象定义的任意索引列表——这是另一个集合类，将在后面的“无序集合”部分讨论。像`-objectsAtIndexes:`这样的方法特别强大；它会返回一个新的`NSArray`对象，该对象是接收者集合的任意子集，通过使用`NSIndexSet`中的索引来选择。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 16 章 ■ 集合模式

### COCOA 集合类组织

Java 集合类被整齐地组织成继承树。基础`Collection`接口有`List`、`Map`和`Set`子接口，这些接口体现在`AbstractCollection`、`AbstractList`、`AbstractMap`和`AbstractSet`类中，并最终实现为我们每天使用的具体集合类。

Objective-C 类几乎没有层次结构。大多数是`NSObject`的直接子类。它们都实现`-count`方法是基于约定，而非正式设计。大多数情况下这几乎没什么区别。Objective-C 确实缺少像 Java 的`addAll(Collection)`这样的方法，因为它没有涵盖所有集合类的基类。

原因之一是许多 Objective-C 集合类实际上是用 C 语言实现的。例如，Core Foundation 的`CFArray`类型实现了`NSArray`，两者可以互换。有关更多详细信息，请参阅第 25 章的“免费桥接”部分。

#### `NSPointerArray`

`NSPointerArray`是一个较新的集合类。它没有不可变的基类，所以在这方面它更像 Java 的`ArrayList`类。使`NSPointerArray`及其同类`NSMapTable`和`NSHashSet`与其他集合类不同的是能够“编程”这些集合。该集合通过一组定义其特性的委托函数进行初始化。`NSPointerArray`对象是使用一个包含一组回调函数的`NSPointerFunctions`对象构建的，这些回调函数将用于操作集合中的值。回调函数包括：`hash`、`isEqual`、`size`、`description`、`acquire`和`relinquish`。当你向数组添加一个值时，该值会被传递给`acquire`函数。当值被移除时，它会通过`relinquish`处理。当比较集合中的值时，候选值会被传递给`isEqual`函数进行比较。

> **注意** 你可能会好奇，当`NSPointerArray`不提供搜索集合的方法时，为什么函数指针集还包括执行`hash`和比较的委托函数。这是为了与映射和集合集合保持一致。后两个类使用相同的指针函数对象，并且确实广泛使用了`hash`和比较函数。

通过使用一组委托函数，集合可以适应各种不同的解决方案。为了便于使用这个新集合，Cocoa 框架包含了预定义的指针函数集，可以通过混合指针函数选项常量来选择它们，其中一些列在表 16-5 中。这些预定义的函数集实现了你可能需要的大部分集合行为。两种最常见的`NSPointerArray`特性有便捷构造函数：

`[NSPointerArray pointerArrayWithStrongObjects]`创建一个常规对象数组，`[NSPointerArray pointerArrayWithWeakObjects]`创建一个使用弱引用的对象数组。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 16 章 ■ 集合模式

**表 16-5.** 常用`NSPointerFunctionsOptions`

| 选项标识符 | 描述 |
|---|---|
| `NSPointerFunctionsStrongMemory` | 值是指针，使用强引用存储。如果未指定内存选项，这是默认的内存选项。 |
| `NSPointerFunctionsZeroingWeakMemory` | 值是指针，使用弱引用存储。 |
| `NSPointerFunctionsObjectPersonality` | 值是对象指针。对象使用`-isEqual:`消息进行比较。`-description`消息用于生成对象描述。如果未指定特性，这是默认的特性。 |
| `NSPointerFunctionsObjectPointerPersonality` | 值是对象指针。通过测试对象指针是否相等来比较对象。`-description`消息用于对象描述。 |
| `NSPointerFunctionsCStringPersonality` | 值是指向 C 字符串的指针。使用`strcmp()`函数比较值，并通过将 C 字符串转换为`NSString`对象来生成描述。 |
| `NSPointerFunctionsIntegerPersonality` | 值是整数。 |
| `NSPointerFunctionsCopyIn` | 添加到集合时复制值。如果值是对象指针，它们必须符合`NSCopying`协议。 |

你也可以通过组合表 16-5 中的一个内存存储选项、一个特性选项以及可选的“复制到”选项来创建一个`NSPointerArray`。清单 16-3 中的代码创建了一个`NSPointerArray`集合，它会复制添加到集合中的对象，并对这些副本保持强引用。

**清单 16-3.** 自定义`NSPointerArray`

```objectivec
NSPointerArray *array = [NSPointerArray pointerArrayWithOptions: (NSPointerFunctionsObjectPersonality
                                                                 |NSPointerFunctionsStrongMemory
                                                                 |NSPointerFunctionsCopyIn) ];
```

甚至还有更多逐渐深奥的选项，允许`NSPointerArray`存储指向 C 结构的指针、包含整个 C 结构的副本、使用 C 语言的`calloc()`和`free()`函数管理内存等等。有关完整列表，请参阅`NSPointerFunctionsOptions`的文档。为了获得最终控制权，如果预定义函数不能满足你的需求，你可以创建自己的`NSPointerFunctions`对象。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 16 章 ■ 集合模式

### 字典集合

`NSDictionary`、`NSMutableDictionary`和`NSMapTable`组织无序的对象对，相当于`java.util.HashMap`。每对包含一个键对象和一个值对象。使用与键相等的对象来访问值。集合中的键是唯一的，但值可以重复。具体而言，Objective-C 和 Java 字典类具有以下共同特征：

- 集合中的键和值是对象引用。
- 键对象不应更改其值。
- 键必须遵守相等性和哈希契约。
- 键是唯一的。为现有键存储新值会用新键/值对替换现有的键/值对。



