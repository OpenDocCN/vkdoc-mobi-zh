# 集合模式

- 单个值可以用不同的键存储多次。
- `NSMapTable` 可以为键和/或值对象使用强引用或弱引用，使其成为 `java.util.WeakHashMap` 的灵活替代品。

有两个关键区别：

- 值对象不能为 `nil`（`null`）。`nil` 值表示键不存在。若要存储 `nil` 值，可从集合中移除该键。
- 键对象始终被复制，且必须遵循 `NSCopying` 协议。集合会保留键对象的副本，而非用于添加键/值对的原始实例。

#### 常用方法

表 16-6 和 16-7 列出了 Java 和 Objective-C 中常见的字典（映射）方法。表 16-6 中的消息不会更改集合，并同时适用于可变和不可变集合。表 16-7 中的消息只能发送给可变字典。

**表 16-6.** 常用字典集合方法  
`java.lang.HashMap` | `NSDictionary` | `NSMapTable`
--- | --- | ---
`size()` | `count` | `count`
`keySet()` | `allKeys` | `allKeysForObject:`
`values()` | `allValues` | `dictionaryRepresentation`
`getObjects:andKeys:` | — | —
`keysSortedByValueUsingSelector:` | — | —
`get(Object)` | `objectForKey:` | `objectForKey:`
`objectsForKeys:notFoundMarker:` | — | —

**表 16-7.** 常用可变字典集合方法  
`java.lang.HashMap` | `NSMutableDictionary` | `NSMapTable`
--- | --- | ---
`put(Object,Object)` | `setObject:forKey:` | `setObject:forKey:`
`putAll(Map)` | `addEntriesFromDictionary:` | `setDictionary:`
`remove(Object)` | `removeObjectForKey:` | `removeObjectForKey:`
`clear()` | `removeAllObjects` | `removeAllObjects`
`removeObjectsForKeys:` | — | —

字典集合不会存储 `nil` 值；`nil` 值用于表示键不存在。Java 语句 `dictionary.contains(key)` 可写作 `[dictionary objectForKey:key] != nil`。如果必须存储一个不带值的键，请使用 `NSNull`。

`-allKeysForObject:` 返回一个数组，包含所有映射到给定值的键。Java 语句 `dictionary.containsValue(object)` 可写作 `[[dictionary allKeysForObject:object] count] != 0`。

`-keysSortedByValueUsingSelector:` 返回一个 `NSDictionary` 的键数组，该数组按特定顺序排序，允许你以明确定义的顺序遍历集合。请参阅“迭代器模式”和“排序”章节。

## `NSDictionary`, `NSMutableDictionary`

除了不允许 `nil`（`null`）值外，`NSMutableDictionary` 几乎与 `java.lang.HashMap` 相同。

最大的区别在于对 `nil` 值的处理以及键的复制。

只要对象可被复制（即遵循 `NSCopying` 协议），且集合保留的副本从未被修改，你就可以使用任何对象作为键。当字典被复制时，所有键都会被复制。

与 `NSArray` 类似，`NSDictionary` 有一些操作条目组的方法。例如，`-objectsForKeys:notFoundMarker:` 接受一个键对象数组，执行批量搜索，并返回一个值对象数组。新数组中的每个条目对应相应键的值，或者“未找到标记”对象。在编写代码以迭代查找、添加或删除对象集合之前，请检查是否有现成的集合方法可以为你完成这项工作。考虑以下语句：

```
[dictionary removeObjectsForKeys:[dictionary allKeysForObject:value]];
```

#### `NSMapTable`

`NSMapTable` 是一个较新的集合类。与 `NSPointerArray` 类似，它没有不可变基类，就像 Java 的 `HashMap` 类一样。同样与 `NSPointerArray` 类似，`NSMapTable` 通过一组定义其行为的委托函数来构造。

`NSMapTable` 实例使用 `NSPointerFunction` 对象或表 16-5 中列出的 `NSPointerFunctionOptions` 来创建。关于 `NSPointerFunctions` 类和指针函数选项的描述，请参阅 `NSPointerArray` 章节。初始化 `NSMapTable` 和初始化 `NSPointerArray` 之间存在两个显著区别。



首先，NSMapTable 只接受 `NSPointerArray` 所支持的函数指针选项中的一小部分。表 16-8 中列出的选项是 `NSMapTable` 唯一支持的选项，同时还包含一些用于 `NSMapTable` 构造函数的同义符号。基本上，映射表只支持对象值，并且这些对象值可以是强引用或弱引用，你还可以选择存储对象的引用或副本。如果你只使用 `NSMapTable` 的选项同义词，就不会意外选到不支持的 `NSPointerFunctions` 选项。

**表 16-8.** `NSMapTable` 选项

| `NSMapTable` 同义词 | `NSPointerArray` 选项 |
| --- | --- |
| `NSMapTableStrongMemory` | `NSPointerFunctionsStrongMemory` |
| `NSMapTableZeroingWeakMemory` | `NSPointerFunctionsZeroingWeakMemory` |
| `NSMapTableCopyIn` | `NSPointerFunctionsCopyIn` |
| `NSMapTableObjectPointerPersonality` | `NSPointerFunctionsObjectPointerPersonality` |

第二个区别在于，`NSMapTable` 对象在初始化时使用两组函数指针：一组用于键，一组用于值。这使得你可以定义一种映射，例如：使用强引用存储键的副本，同时使用弱引用存储值；或者引用键并存储值的副本；或者使用弱键和弱值；或者任何其他对你的应用程序有意义的组合。

对于最常见的配置，有四个便利构造方法：

`+mapTableWithStrongToStrongObjects`、`+mapTableWithWeakToStrongObjects`、
`+mapTableWithStrongToWeakObjects` 和 `+mapTableWithWeakToWeakObjects`。

### 集合（Set Collections）

`NSSet`、`NSMutableSet`、`NSCountedSet`、`NSIndexSet` 和 `NSHashTable` 组织无序对象的集合，大致相当于 `java.util.HashSet` 和 `java.util.BitSet`。集合类遵循集合的数学概念：一个对象要么是集合的成员，要么不是。对象不按任何特定顺序组织，也不可寻址。你可以向集合中添加对象、测试其是否存在，以及移除它。`NSIndexSet` 将相同的概念应用于整数值，而 `NSCountedSet` 是一个特殊的集合，允许一个对象在集合中出现多次。具体来说，Objective-C 和 Java 的集合类具有以下共同特征：

- 集合中的值是对象引用或整数。
- 一个值在集合中只能存储一次。添加重复值不会有任何效果（`NSCountedSet` 是此规则的例外）。

#### 通用方法

表 16-9 和 16-10 列出了 Java 和 Objective-C 中适用于对象集合的通用集合方法。表 16-9 中的消息不会改变集合，并且对于可变和不可变集合都已实现。表 16-10 中的消息只能发送给可变集合。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 16 章 ■ 集合模式**

**表 16-9.** 通用集合方法

| `java.lang.HashSet` | `NSSet` | `NSHashTable` |
| --- | --- | --- |
| `size()` | `count` | `count` |
| `toArray()` | `allObjects` | `allObjects` |
|  | `anyObject` | `anyObject` |
| `contains(Object)` | `containsObject:` | `containsObject:` |
| `containsAll(Collection)` | `isSubsetOfSet:` | `isSubsetOfHashTable:` |
|  | `intersectsSet:` | `intersectsHashTable:` |
|  | `setRepresentation` |  |

**表 16-10.** 通用可变集合方法

| `java.lang.HashSet` | `NSMutableSet` | `NSHashTable` |
| --- | --- | --- |
| `add(Object)` | `addObject:` | `addObject:` |
| `remove(Object)` | `removeObject:` | `removeObject:` |
| `clear()` | `removeAllObjects` | `removeAllObjects` |
| `addAll(Collection)` | `addObjectsFromArray:` | `addAll(Collection)` |
|  | `unionSet:` | `unionHashTable:` |
| `removeAll(Collection)` | `minusSet:` | `minusHashTable:` |
|  | `intersectSet:` | `intersectHashTable:` |
|  | `setSet:` |  |

Objective-C 类包含一些高级方法，例如 `-intersectSet:`，使得执行集合运算变得容易。它们还包含有趣的 `-anyObject` 消息，该方法返回集合中的一个任意成员。

#### `NSSet`、`NSMutableSet`

`NSSet` 类在行为上几乎与 `java.util.HashSet` 相同。语句 `set.isEmpty()` 可以替换为 `[set count]==0`。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 16 章 ■ 集合模式**



可变集合对象可以通过初始容量进行初始化，这有助于优化其性能。在创建大型集合时，准确的初始容量最为有用。

#### `NSCountedSet`

`NSCountedSet` 是 `NSMutableSet` 的子类，允许将单个对象多次添加到集合中。本质上，它将添加到集合中的每个对象视为一个独特但不可区分的实体。

在内部，该集合维护对对象的单个引用以及该对象被添加到集合中的次数计数。要移除一个对象，集合必须为之前收到的每个 `-addObject:` 消息接收一次 `-removeObject:` 消息。`-countForObject:` 方法将返回该对象收到的 `-addObject:` 消息数量减去 `-removeObject:` 消息数量所得的值。

#### `NSIndexSet`

`NSIndexSet` 是一个特殊的集合类，用于维护一组整数值，与 `java.util.BitSet` 非常相似。

表 16-11 和 16-12 列出了 Java 和 Objective-C 中的常用方法。

**表 16-11.** 常用索引集合方法

| `java.util.BitSet` | `NSIndexSet` |
| --- | --- |
| `cardinality()` | `count` |
| | `countOfIndexesInRange:` |
| `get(int)` | `containsIndex:` |
| | `containsIndexes:` |
| | `containsIndexesInRange:` |
| `intersects(BitSet)` | `intersectsIndexesInRange:` |
| | `firstIndex` |
| `length()` | `lastIndex` |
| | `indexLessThanIndex:` |
| | `indexLessThanOrEqualToIndex:` |
| `nextBitSet(int)` | `indexGreaterThanOrEqualToIndex:` |
| | `indexGreaterThanIndex:` |
| `get(int,int)` | `getIndexes:maxCount:inIndexRange:` |

**表 16-12.** 常用可变索引集合方法

| `java.util.BitSet` | `NSMutableIndexSet` |
| --- | --- |
| `set(int)` | `addIndex:` |
| `or(BitSet)` | `addIndexes:` |
| `set(int,int,true)` | `addIndexesInRange:` |
| `clear(int)` | `removeIndex:` |
| `andNot(BitSet)` | `removeIndexes:` |
| `clear()` | `removeAllIndexes` |
| `clear(int,int)` | `removeIndexesInRange:` |
| | `shiftIndexesStartingAtIndex:by:` |

`NSIndexSet` 和 `java.util.BitSet` 的共同功能比其他大多数集合类要少。Java 类有多个用于翻转位和执行布尔运算的方法，而 Objective-C 类则缺少这些方法。

`NSIndexSet` 的主要用途是高效地封装有序集合的任意子集。`NSArray` 类和用户界面显示类广泛使用它。例如，表格视图将用户的当前选择作为一个 `NSIndexSet` 返回，以标识所选择的行。该接口有许多方法，例如 `-indexLessThanIndex:`，使得可以轻松地沿任意方向遍历集合。

#### `NSHashTable`

`NSHashTable` 是 `NSMapTable` 的姊妹类。与 `NSMapTable` 类似，它使用一组定义其行为的委托函数构建。同样与 `NSMapTable` 类似，它只接受有限数量的函数指针选项，这些选项列在表 16-13 中。有关这些函数指针选项的说明，请参阅 `NSMapTable` 和 `NSPointerArray` 部分。

**表 16-13.** `NSMapTable` 选项

| `NSHashTable` 同义词 | `NSPointerArray` |
| --- | --- |
| `NSHashTableStrongMemory` | `NSPointerFunctionsStrongMemory` |
| `NSHashTableZeroingWeakMemory` | `NSPointerFunctionsZeroingWeakMemory` |
| `NSHashTableCopyIn` | `NSPointerFunctionsCopyIn` |
| `NSHashTableObjectPointerPersonality` | `NSPointerFunctionsObjectPointerPersonality` |

`NSHashTable` 和 `NSMapTable` 之间唯一显著的区别是 `NSHashTable` 是一个集合，只需要一组函数指针来定义其行为。

> **注意：** 不要混淆 `NSHashTable`（类名）和 `NSHashTable`（C 类型）。后者属于较旧的 C API，用于实现底层哈希表。Objective-C 的 `NSHashTable` 类包含了其大部分功能。

### 组合模式

组合模式的一个方面是能够通过单个对象与对象组进行交互。`NSArray` 和 `NSSet` 类各自提供了两个方法，允许您向集合中的所有对象发送一条消息：

- `(void)makeObjectsPerformSelector:(SEL)aSelector`
- `(void)makeObjectsPerformSelector:(SEL)aSelector withObject:(id)anObject`

清单 16-4 中的代码演示了如何向集合中的每个对象发送消息。

**清单 16-4.** 组合消息

**Java**

```java
ArrayList<Example> array = …
for ( Example example : array ) {
   example.setDelegate(this);
}
```

**Objective-C**

```objc
NSArray *array = …
[array makeObjectsPerformSelector:@selector(setDelegate:) withObject:self];
```

### 集合相等性约定

集合的搜索和排序功能依赖于对象以一致且合理的方式响应 `-isEqual:` 消息。这被称为相等性约定。集合和字典通过哈希表定位对象。这依赖于对象的 `-hash` 消息返回一个一致的值，该值与 `-isEqual:` 的响应具有可预测的关系。这被称为哈希约定。

相等性约定和哈希约定与 Java 中的相同。相等性约定如下：

1.  具有等效值的两个对象必须从 `-isEqual:` 返回 `YES`，否则返回 `NO`。
2.  相等的两个对象指针始终被视为相等且相同。因此，`[self isEqual:self]` 始终返回 `YES`。
3.  有效的对象指针和 `nil` 对象指针始终被视为不相等。语句 `[self isEqual:nil]` 必须始终返回 `NO`。
4.  `[a isEqual:b]` 必须始终返回与 `[b isEqual:a]` 相同的值。

必须按顺序考虑相等性约定的规则。例如，在两个对象指针都为 `nil` 的特殊情况下，规则 2 优先于规则 3 和 4；`nil` 始终等于 `nil`。

哈希约定很简单，也与 Java 的一致：

1.  相等的两个对象（`[a isEqual:b]==YES`）必须返回相同的哈希值（`[a hash]==[b hash]`）。
2.  理想情况下，不同的对象（`[a isEqual:b]==NO`）应返回显著不同且在大整数范围内均匀分布的哈希值。

`-isEqual:` 和 `-hash` 方法在 `NSObject` 中定义，因此每个对象都继承它们。然而，`-hash` 的默认实现并不适用于集合和字典。如果您创建了一个计划在集合中使用的 `NSObject` 子类，则应重写 `-isEqual:` 并实现相等性约定。如果该对象将存储在集合中或用作字典中的键，则还必须重写 `-hash` 以实现哈希约定。清单 16-5 显示了正确实现的相等性和哈希方法。

**清单 16-5.** 相等性与哈希方法

```objc
@interface AircraftIdentifier : NSObject {
    NSString *registrationNumber;
    unsigned int transponderCode;
}
@property (readonly) NSString *registrationNumber;
@property unsigned int transponderCode;
- (id)initWithRegistrationNumber:(NSString*)registration;
@end

@implementation AircraftIdentifier
@synthesize registrationNumber, transponderCode;

- (id)initWithRegistrationNumber:(NSString*)registration
{
    self = [super init];
    if (self != nil) {
        registrationNumber = registration;
        transponderCode = 1200; // VFR 模式下的默认应答机编码
    }
    return self;
}

- (BOOL)isEqual:(id)object
{
    if (self==object) // 恒等规则
        return YES;
    if (object==nil) // nil 规则
        return NO;
    if (![object isKindOfClass:[AircraftIdentifier class]])
        return NO; // 类不可识别

    // 如果航空器识别符的注册号相同，则它们相等
    AircraftIdentifier *r = (AircraftIdentifier*)object;
    return [registrationNumber isEqualToString:r->registrationNumber];
}

- (NSUInteger)hash
{
    // 返回用于判断相等性的属性的哈希值
    return [registrationNumber hash];
}

@end
```



在`AircraftIdentifier`类中，分配给`transponderCode`的值在比较两个标识符对象的相等性时不被视为重要因素。这是因为该值可能会变化，但并不会实质性地改变飞机的身份。如何确定类的相等性会有所不同；只需记住，`-hash`方法必须始终返回与`-isEqual:`一致的值。

**比较集合**

集合本身可以与同类集合进行比较。表 16-14 列出了集合比较方法。

***表 16-14.** 集合比较方法*

| 类 | 方法 |
| --- | --- |
| `NSArray`, `NSMutableArray` | `-isEqualToArray:` |
| `NSDictionary`, `NSMutableDictionary` | `isEqualToDictionary:` |
| `NSSet`, `NSMutableSet`, `NSCountedSet` | `isEqualToSet:` |
| `NSIndexSet`, `NSMutableIndexSet` | `isEqualToIndexSet:` |
| `NSHashTable` | `isEqualToHashTable:` |

如果两个对象集合包含相同数量的对象，并且集合中的每个对象在向另一个集合中的对应对象发送`-isEqual:`消息时都返回`YES`，则认为这两个集合相等。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 16 章 ■ 集合模式

某些集合具有不等式比较，用于判断一个集合是否是另一个集合的子集或超集。这些方法包括`-[NSSet isSubsetOfSet:]`、`-[NSHashTable isSubsetOfHashTable:]`和`-[NSIndexSet containsIndexes:]`。

**迭代器模式**

可能一直以来最常见的编程任务就是遍历集合中的元素，对每个成员执行某些测试或操作。Java 和 Objective-C 都意识到了这一点，并做出了重要的语言变化，旨在简化这种繁琐且重复的（无意双关）编程模式。你可以使用新的语法、遗留的枚举类，或直接访问每个成员对象来遍历集合。本节将解释每种方法的实现方式，以及为你的类添加枚举支持所需的步骤。

Objective-C 枚举与 Java 迭代器的主要区别在于：

*   Objective-C 集合没有类型化。
*   在枚举期间不得修改 Objective-C 集合。

Java 为集合添加了参数化类型，消除了使用集合类所需的大量繁琐操作。参数化集合确保添加到集合中的所有对象都是正确的类型，并自动将从集合中提取的对象转换为基础类型。Objective-C 不需要此类构造，因为它在赋值期间不验证对象的类。所有集合类都接受并返回匿名的`id`对象标识符，编译器假定该标识符可以自由地与任何类互换。如果你希望确保添加到集合中或从集合中获取的对象是正确的类，请添加断言。有关断言的更多信息，请参见第 14 章。

“集合并发”部分描述了如何处理在枚举期间无法修改集合的限制。这也意味着 Objective-C 枚举类没有修改集合的方法。

**使用快速枚举**

快速枚举是在 Objective-C 2.0 中引入的。它是一种简写的`for(...)`循环语法，用于枚举集合中的对象。Java 有非常类似的东西，称为 For-Each 循环语法。两者的示例见代码清单 16-6。两者的用法、语法和行为几乎相同。

**代码清单 16-6.** 快速枚举语法

```java
Java
for ( Object object : collection ) {
    ...
}
```

```objectivec
Objective-C
for ( id object in collection ) {
    ...
}
```

快速枚举中的“快速”不仅仅是对节省编码时间的委婉说法。快速枚举实际上很快。快速枚举接口允许集合批量获取对象，从而减少开销并提高性能。在除少数情况外的所有情况下，快速枚举是遍历集合中对象最快的方式。



### 使用枚举器进行快速枚举

这使得快速枚举成为遍历数组的首选方法，因为它最容易编写、最易读且最高效。

#### 使用枚举器

在快速枚举出现之前，Objective-C 集合是通过`NSEnumerator`类进行枚举的。

`NSEnumerator`在功能上与`java.util.Iterator`非常相似。与`Iterator`的两个方法`next()`和`hasNext()`不同，`NSEnumerator`只有一个`-nextObject`方法。`-nextObject`消息会返回一个非`nil`的指针，直到枚举结束。之所以能做到这一点，是因为支持`NSEnumerator`的集合类不允许存储`nil`值。那些允许存储`nil`值的集合（如`NSPointerArray`）不支持`NSEnumerator`。清单 16-7 中的代码演示了如何使用枚举器对象。

**清单 16-7. 使用`NSEnumerator`遍历集合**

**Java**

```
ArrayList<Object> array = …

for (Iterator<Object> i = array.iterator(); i.hasNext(); ) {
    Object object = i.next();
    …
}
```

**Objective-C**

```
NSArray *array = …
NSEnumerator *e = [array objectEnumerator];
id object;
while ( (object=[e nextObject])!=nil ) {
    …
}
```

你获取`NSEnumerator`对象的方式与在 Java 中获取`Iterator`一样——通过请求集合为你提供一个。集合可能提供几种不同类型的枚举器。

`NSDictionary`会生成一个`-keyEnumerator`和一个`-objectEnumerator`对象，它们分别用于遍历其键对象或值对象。`NSArray`类提供了`-objectEnumerator`和`-reverseObjectEnumerator`对象。

有一些`NSEnumerator`的特殊用途子类，例如`NSDirectoryEnumerator`。除了遍历目录内容之外，它还实现了用于控制递归和获取当前文件属性的额外方法。关于`NSDirectoryEnumerator`的示例，请参见第 11 章。

`NSEnumerator`定义的唯一另一个方法是`-allObjects`。这个消息的名称有点误导，因为它返回的是枚举器尚未返回的对象数组。它本应命名为`-remainingObjects`。

枚举器对象无法重置或重启。一旦完成枚举，它们就变为无效状态。枚举器对象在枚举结束之前会保留对其所遍历的集合的引用。

---

[www.it-ebooks.info](http://www.it-ebooks.info/)

