# 8. 数组

**摘要：** `NSArray` 是一个用于在列表中组织对象的类。`NSArray` 可以维护对象的索引、搜索对象以及枚举遍历列表。枚举是逐个遍历列表中的每个项目并对每个项目执行操作的过程。



## NSArray

`NSArray` 是一个用于以列表形式组织对象的类。`NSArray` 可以维护对象的索引、搜索对象以及遍历列表。枚举就是逐个遍历列表中的元素，并对每个元素执行操作的过程。

要创建数组，你需要使用逗号分隔的对象列表，将其括在方括号中，并以 `@` 符号开头。

```
NSArray *numbers = @[@-2, @-1, @0, @1, @2];
```

```
NSArray *letters = @[@"A", @"B", @"C", @"D", @"E", @"F"];
```

`NSArray` 对象 `numbers` 包含一个 `NSNumber` 对象列表，而 `letters` 则包含一个字符串列表。任何对象都可以放入 `NSArray` 对象中，但不能放入诸如 `NSInteger` 这样的基本类型。

### 引用对象

将对象放入数组，是为了以后能方便地获取对这些对象的引用。获取这些引用的一般方法是向数组发送 `objectAtIndex:` 消息。以下是如何从 numbers 数组的第二个位置获取数字对象引用的示例：

```
NSNumber *num = [numbers objectAtIndex:1];
```

如果你知道想要列表中的最后一个对象，可以使用 `lastObject` 来返回列表中的最后一个对象。

> **注意**：Objective-C 中的数组索引从 0 开始。

```
NSNumber *lastNum = [numbers lastObject];
```

有时你可能已经拥有待查询对象的引用，但想要找出该对象在数组中的位置所对应的索引号。你可以使用 `indexOfObject:` 来获取此信息。

```
NSUInteger index = [numbers indexOfObject:num];
```

### 枚举

枚举就是逐个遍历列表中的元素。通常，你会对每个元素执行某种操作，例如将对象的内容写入日志或修改对象的属性。

代码块（Blocks），即匿名函数，用于对数组执行枚举操作。代码块是不绑定到对象上的函数。你可以在封闭代码行的大括号内定义一个代码块。代码块可以像对象一样被处理，这意味着你可以将代码块传递给枚举方法，就像传递变量或对象一样。

> **注意**：代码块本身值得单独讲解，它们不仅用于数组。关于如何使用代码块的更多细节将在第 20 章中介绍。

假设你想遍历数字数组，并打印出每个数字的平方值。你可以使用 `NSArray enumerateObjectsUsingBlock:` 方法遍历列表。该方法会为你提供一个当前对象的引用，你可以利用它来执行这个简单的操作。

```
[numbers enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    NSLog(@"obj ^ 2= %f", [obj floatValue] * [obj floatValue]);
}];
```

冒号后面的所有代码都是代码块代码。这个代码块以 `^` 符号开头。然后你会看到一个逗号分隔的参数列表，后面跟着大括号。大括号内的代码就是代码块，而括号中声明的参数是代码块可以引用的变量。

## NSMutableArray

很多时候，当程序执行代码时，你需要能够向数组中添加和删除项目。你可能正在维护一个操作项列表或视频游戏角色列表。当需要这样做时，你可以使用 `NSMutableArray`。

`NSMutableArray` 能做 `NSArray` 能做的所有事情，只是它额外提供了更改数组内容的能力。你可以添加和删除项目，并对可变数组中的对象执行其他类型的操作。

不过，你不能在此处使用创建数组的快捷方式，`NSMutableArray` 要求你使用构造方法，如下所示：

```
NSMutableArray *mArray = [NSMutableArray arrayWithArray:@[@-2, @-1, @0]];
```

上面使用的构造方法是 `arrayWithArray:`，你只需将一个 `NSArray` 对象传递给这个构造方法即可开始。

要向可变数组添加对象，请使用 `addObject:` 消息。

```
[mArray addObject:@1];
```

要删除对象，你必须使用 `removeObject:` 消息，并传递一个指向要删除对象的引用。

```
[mArray removeObject:@1];
```

如果你想交换两个对象的位置，可以使用 `exchangeObjectAtIndex:withObjectAtIndex:` 方法。

```
[mArray exchangeObjectAtIndex:0 withObjectAtIndex:1];
```

这将把位置 0 上的内容与位置 1 上的内容互换。

还有许多其他变体方法可供你使用。你可以删除所有项目、将项目数组添加到可变数组中，或者在数组的特定起始点插入项目或项目数组。

