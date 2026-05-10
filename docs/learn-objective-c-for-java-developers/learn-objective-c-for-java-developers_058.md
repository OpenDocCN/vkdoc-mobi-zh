# 第 16 章 ■ 集合模式

### 有序集合

`NSArray`、`NSMutableArray` 和 `NSPointerArray` 组织有序的值集合，相当于 `java.util.ArrayList`。具体来说，Objective-C 和 Java 的数组类具有以下共同特性：

- 集合中的值是对象引用。
- 值通过索引进行访问。
- 同一个值可以存储在多个索引位置。
- 新值可以追加到数组末尾，也可以在现有索引处插入，并将后续值向后移动一个索引。不能在数组末尾之后插入值。
- 移除一个值会使所有后续值向前移动以填补空缺的索引。
- `NSArray` 集合可以进行搜索，以定位已知值的索引。

还存在一些关键差异：

- `NSArray` 对象不能用于存储 `nil`（`null`）值。因此，像 `-setCount:` 这样会用 `nil` 值填充数组的操作未实现。
- `NSPointerArray` 对象不会搜索其内容中的值。因此，诸如 `containsObject`、`indexOfObject:` 和 `removeObject:` 这样的方法未实现。你可以遍历其内容来查找值。
- `NSPointerArray` 可以存储比对象指针值更多的内容。
- `NSMutableArray` 可以用预定的容量进行初始化。但除此之外，数组的容量是不透明的，除了 `NSPointerArray` 实现的唯一方法 `-compact`。

#### 常用方法

表 16-3 和 16-4 列出了 Java 和 Objective-C 中常见的数组方法。表 16-3 中的消息不会改变集合，并且对可变和不可变集合都已实现。表 16-4 中的消息只能发送给可变数组。

**表 16-3.** 常见数组集合方法

| `java.lang.ArrayList` | `NSArray` | `NSPointerArray` |
|----------------------|-----------|------------------|
| `size()`             | `count`   | `count`          |
| `get(int)`           | `objectAtIndex:` | `pointerAtIndex:` |
|                      | `objectsAtIndexes:` |                  |
| `contains(Object)`   | `containsObject:` |                  |
| `indexOf(Object)`    | `indexOfObject:` |                  |
|                      | `indexOfObject:inRange:` |       |
|                      | `indexOfObjectIdenticalTo:` |    |
|                      | `indexOfObjectIdenticalTo:inRange:` | |
| `lastIndexOf(Object)`| `lastObject:` |                  |
| `toArray()`          | `getObjects:` | `allObjects`   |
| `subList(int,int)`   | `getObjects:range:` |              |

**表 16-4.** 常见可变数组集合方法

| `java.lang.ArrayList` | `NSMutableArray` | `NSPointerArray` |
|----------------------|------------------|------------------|
| `add(Object)`        | `addObject:`     | `addPointer:`    |
| `addAll(Collection)` | `addObjectsFromArray:` |              |
| `add(int,Object)`    | `insertObject:atIndex:` | `insertPointer:atIndex:` |
|                      | `insertObjects:atIndexes:` |          |
|                      | `setCount:`      |                  |
| `clear()`            | `removeAllObjects` |                  |
|                      | `removeLastObject` |                  |
| `remove(Object)`     | `removeObject:`  |                  |
|                      | `removeObject:inRange:` |               |
| `remove(int)`        | `removeObjectAtIndex:` | `removePointerAtIndex:` |
|                      | `removeObjectsAtIndexes:` |            |
|                      | `removeObjectIdenticalTo:` |           |
|                      | `removeObjectIdenticalTo:inRange:` |  |
| `removeRange(int,int)` | `removeObjectsFromIndices:numIndices:` | |


