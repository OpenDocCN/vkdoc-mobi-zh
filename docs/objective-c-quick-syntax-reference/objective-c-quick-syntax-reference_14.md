# 9. 字典

**摘要**：`NSDictionary` 是一个用于通过键和值以列表形式组织对象的类。`NSDictionary` 可以维护对象的索引，并让你在拥有正确键的情况下检索对象。通常，键是一个 `NSString` 对象，而值则是你正在索引的任何类型的对象。

## NSDictionary

`NSDictionary` 是一个用于通过键和值以列表形式组织对象的类。`NSDictionary` 可以维护对象的索引，并让你在拥有正确键的情况下检索对象。通常，键是一个 `NSString` 对象，而值则是你正在索引的任何类型的对象。

要创建字典，你需要使用逗号分隔的键值对列表，将其括在大括号中，并以 `@` 符号开头。

```
NSDictionary *d1 = @{@"one": @1, @"two": @2, @"three": @3};
```

这将创建一个 `NSNumber` 对象的字典，你可以使用它们的字符串键进行引用。因此，键字符串 `@"one"` 可用于检索 `NSNumber` 对象 `1`。

### 引用对象

将对象放入字典，是为了能基于键高效地获取对这些对象的引用。获取这些引用的一般方法是向字典发送 `objectForKey:` 消息。以下是如何获取由键 `@"one"` 引用的数字的示例：

```
NSNumber *n1 = [d1 objectForKey:@"one"];
```

### 枚举

枚举就是逐个遍历列表中的元素。通常，你会对每个元素执行某种操作，例如将对象的内容写入日志或修改对象的属性。

你可以用几乎与数组相同的方式枚举字典。但你会获得字典中每个键以及每个对象的引用。

```
[d1 enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
    NSLog(@"key = %@, value = %@", key, obj);
}];
```

与第 8 章中讨论的数组枚举过程一样，代码块声明以 `^` 字符开头，代码块代码包含在大括号 `{ }` 中。

以下是使用此消息将生成的输出：

```
key = one, value = 1
key = two, value = 2
key = three, value = 3
```



## NSMutableDictionary

`NSDictionary` 是一个不可变对象，因此一旦你创建了一个 `NSDictionary` 对象，就无法向字典中添加或删除条目。如果你需要向字典中添加或删除条目，则必须使用 `NSMutableDictionary` 类。

不过，这里不能使用数组创建的快捷方式，`NSMutableDictionary` 要求你使用构造器，像这样：

```
NSMutableDictionary *md1 = [[NSMutableDictionary alloc] init];
```

最简单的做法是遵循 `alloc` 和 `init` 模式来创建一个空字典，然后你就可以向其中添加对象。当你准备向字典中添加对象时，你需要一个键（key）和要添加的值（value）。这两个参数将被提供给 `setObject: forKey:` 方法。

```
[md1 setObject:@4 forKey:@"four"];
```

若要移除一个对象，发送 `removeObjectForKey:` 消息并提供相应的键。

```
[md1 removeObjectForKey:@"four"];
```

你可以通过发送 `removeAllObjects` 消息来移除可变字典中的所有对象。

```
[md1 removeAllObjects];
```

