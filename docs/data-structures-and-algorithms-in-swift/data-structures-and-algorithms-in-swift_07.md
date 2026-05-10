# `remove(at:)`

该函数用于移除指定索引处的元素。

```
func remove(at position: Int) {
if head == nil {
return
}
var h = head
if (position == 0) {
head = h?.next
return
}
for _ in 0..<position-1 where h != nil {
h = h?.next
}
if h == nil || h?.next == nil {
return
}
let nextToNextNode = h?.next?.next
h?.next = nextToNextNode
}
```

首先，我们检查 `head` 是否为空，若为空则无需移除任何元素。接着，我们创建一个头节点的实例。如果 `position` 为 0，则移除头节点并将下一个节点的值赋给头节点，然后返回；否则，我们通过不断赋值头节点，从 0 循环到 `position`。

让我们通过一个示例来理解：

```
print("初始列表: \(newList)")
newList.remove(at: 2)
print("已移除索引 2: \(newList)")
newList.remove(at: 0)
print("已移除索引 0: \(newList)")
```

输出结果如下：

```
初始列表: 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 8
已移除索引 2: 1 -> 2 -> 4 -> 5 -> 6 -> 8
已移除索引 0: 2 -> 4 -> 5 -> 6 -> 8
```

## 双向链表

如前所述，双向链表是一种每个节点都包含指向前一个节点和下一个节点引用的链表。接下来，我们将创建 `DoublyLinkedList` 类来了解其工作原理。首先，为双向链表创建 `Node` 类。

```
public class DoubleNode {
var value: nodeType
var next: DoubleNode?
weak var previous: DoubleNode?
init(value: nodeType) {
self.value = value
}
}
extension DoubleNode: CustomStringConvertible {
public var description: String {
guard let next = next else {
return "\(value)"
}
return "\(value) -> " + String(describing: next) + " " }
}
```

每个节点都关联一个值、一个指向下一个节点的指针，以及一个指向前一个节点的指针。为了防止内存循环，我们将前一个指针声明为弱引用（weak reference）。

创建 `DoubleNode` 类之后，就可以继续创建 `DoublyLinkedList` 类了。我们声明了 `nodeAt` 函数作为辅助函数，用于获取给定索引处的元素。

```
public class DoublyLinkedList {
var head: DoubleNode?
private var tail: DoubleNode?
public var isEmpty: Bool {
return head == nil
}
public func nodeAt(index: Int) -> DoubleNode? {
if index >= 0 {
var node = head
var i = index
while node != nil {
if i == 0 { return node }
i -= 1
node = node!.next
}
}
return nil
}
}
```

### 追加元素

以下函数用于向双向链表添加元素：

```
func insert(node: DoubleNode, at index: Int) {
if index == 0,
tail == nil {
head = node
tail = node
} else {
guard let nodeAtIndex = nodeAt(index: index) else {
print("索引越界。")
return
}
if nodeAtIndex.previous == nil {
head = node
}
node.previous = nodeAtIndex.previous
nodeAtIndex.previous?.next = node
node.next = nodeAtIndex
nodeAtIndex.previous = node
}
}
```

### 移除节点方法

```
public func remove(node: DoubleNode) -> listType {
let previousNode = node.previous
let nextNode = node.next
if let prev = previousNode {
prev.next = nextNode
} else {
head = nextNode
}
nextNode?.previous = previousNode
if nextNode == nil {
tail = previousNode
}
node.previous = nil
node.next = nil
return node.value
}
```

### `remove(at:)`

```
func remove(at index: Int) {
var toDeleteNode = nodeAt(index: index)
guard toDeleteNode != nil else {
print("索引越界。")
return
}
let previousNode = toDeleteNode?.previous
let nextNode = toDeleteNode?.next
if previousNode == nil {
head = nextNode
}
if toDeleteNode?.next == nil {
tail = previousNode
}
previousNode?.next = nextNode
nextNode?.previous = previousNode
toDeleteNode = nil
}
```

## 本章小结

在本章中，你学习了链表的基本结构、如何在 Swift 中创建单向链表和双向链表，以及如何使用 `remove`、`insert` 和 `append` 方法。下一章，你将学习哈希表这种数据结构类型。

