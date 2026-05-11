# 你拥有所创建的任何对象的所有权

当你使用的方法名以下列内容开头时，意味着你在创建一个对象并拥有其所有权。

*   `alloc`
*   `new`
*   `copy`
*   `mutableCopy`

让我们通过一些示例源码来看看如何创建一个对象。下面的示例使用了 `alloc` 方法来创建并拥有一个对象的所有权。

```
/*
 * 你创建一个对象并拥有所有权。
 */

id obj = [[NSObject alloc] init];

/*
 * 现在，你拥有了该对象的所有权。
 */
```

通过调用 `NSObject` 的类方法 `alloc`，你创建了一个对象并获得了它的所有权。变量 `obj` 持有一个指向所创建对象的指针。你也可以使用类方法 `new` 来创建它。`[NSObject new]` 和 `[[NSObject alloc] init]` 的功能完全相同。

```
/*
 * 你创建一个对象并拥有所有权。
 */

id obj = [NSObject new];

/*
 * 现在你拥有了该对象的所有权。
 */
```

`NSObject` 的实例方法 `copy` 创建一个对象的副本，该对象的类必须遵循 `NSCopying` 协议，并且 `copyWithZone:` 方法必须正确实现。同样地，`NSObject` 的实例方法 `mutableCopy` 创建一个对象的可变副本，该对象的类必须遵循 `NSMutableCopying` 协议，并且 `mutableCopyWithZone:` 方法必须正确实现。`copy` 和 `mutableCopy` 的区别类似于 `NSArray` 和 `NSMutableArray` 的区别。这些方法创建新对象的方式与 `alloc` 和 `new` 相同；因此，你拥有其所有权。

如前所述，当你使用名称以 `alloc`、`new`、`copy` 或 `mutableCopy` 开头的方法时，你会创建一个对象并拥有其所有权。以下是方法名的一些示例。

*   `allocMyObject`
*   `newThatObject`
*   `copyThis`
*   `mutableCopyYourObject`

然而，这种命名约定并不适用于以下方法。

*   `allocate`
*   `newer`
*   `copying`
*   `mutableCopyed`

**注意：** 请对方法名使用驼峰命名法。驼峰命名法是一种书写复合词或短语的实践，其中各元素之间无空格连接，且每个元素的首字母在复合词中大写。对于方法名，首字母应为小写，即小驼峰命名法。请参考维基百科的“CamelCase”条目 [`http://en.wikipedia.org/wiki/CamelCase`](http://en.wikipedia.org/wiki/CamelCase)。

