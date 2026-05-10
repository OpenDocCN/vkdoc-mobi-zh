# 集合模式（Collection Patterns）

创建另一个字典的不可变浅拷贝。

- `[[NSDictionary alloc] initWithDictionary:copyItems:]`  
  若`copyItems`为`NO`，则等同于`[NSDictionary dictionaryWithDictionary:]`。  
  若`copyItems`为`YES`，则新字典是原字典的深拷贝，通过向每个值对象发送`-copyWithZone:`消息实现。键对象始终被拷贝。

- `+[NSDictionary dictionaryWithObject:forKey:]`  
  创建包含单个键/值对的不可变字典。

- `+[NSDictionary dictionaryWithObjects:forKeys:]`  
  从两个数组（一个包含键，另一个包含值）创建不可变字典。

- `+[NSDictionary dictionaryWithObjects:forKeys:count:]`  
  从两个 C 数组（一个包含键，另一个包含值）创建不可变字典。

- `+[NSDictionary dictionaryWithObjectsAndKeys:]`  
  从可变参数列表中的任意数量的值/键对创建不可变字典。列表以单个`nil`值终止。

- `+[NSSet setWithSet:]`  
  创建另一个集合的不可变浅拷贝。

- `[[NSSet alloc] initWithSet:copyItems:]`  
  若`copyItems`为`NO`，则等同于`+[NSSet setWithSet:]`。  
  若`copyItems`为`YES`，则新集合是深拷贝，通过向集合中的每个对象发送`-copyWithZone:`消息实现。

- `+[NSSet setWithArray:]`  
  从数组创建不可变集合。

- `+[NSSet setWithObject:]`  
  创建包含单个对象的不可变集合。

- `+[NSSet setWithObjects:]`  
  创建包含可变参数列表中对象的不可变集合。列表以`nil`对象值终止。

- `+[NSSet setWithObjects:count:]`  
  从对象指针的 C 数组创建不可变集合。参数是数组第一个元素的地址和元素数量。

- `-[NSSet setByAddingObject:]`  
  创建新的不可变集合，它是接收者的浅拷贝加上一个额外对象。

- `-[NSSet setByAddingObjectsFromSet:]`  
  创建新的不可变集合，它是接收者集合与参数集合的并集。

- `-[NSSet setByAddingObjectsFromArray:]`  
  创建新的不可变集合，它是接收者集合与数组中对象的并集。

- `-[NSSet filteredSetUsingPredicate:]`  
  创建新的不可变集合，包含接收者集合中匹配谓词表达式的对象。

- `+[NSIndexSet indexSetWithIndex:]`  
  创建包含单个索引的不可变索引集合。

- `+[NSIndexSet indexSetWithIndexesInRange:]`  
  创建包含给定范围内所有索引的不可变索引集合。

- `[[NSIndexSet alloc] initWithIndexSet:]`  
  创建另一个索引集合副本的不可变索引集合。

从其他集合创建的集合通常通过简单地复制对象指针来制作浅拷贝。这是高度优化的，通常非常快。通常的做法是创建一个可变集合来组装一组对象，然后返回一个不可变副本，如清单 16-1 所示。这类似于创建`java.lang.StringBuilder`对象，构建字符串，然后通过`StringBuilder.toString()`返回不可变的`String`。

**清单 16-1. 返回不可变集合**

```objc
- (NSArray*)guestList
{
    // 组装嘉宾数组
    NSMutableArray *scratchArray = [NSMutableArray new];
    for ( … ) {
        …
        [scratchArray addObject:…];
    }
    // 返回不可变的嘉宾数组
    return [NSArray arrayWithArray:scratchArray];
}
```

少数构造函数会制作深拷贝。这些构造函数有一个`copyItems`参数。当字典被复制时，键对象总是被拷贝。

由于可变子类继承了其超类的所有方法，可变集合类可以使用表 16-2 中的任何类方法来预填充新的可变集合。当你有一个需要修改的不可变集合时，通常会这样做，如清单 16-2 所示。

**清单 16-2. 创建不可变集合的可变副本**

```objc
- (void)hardwareNotification:(NSNotification*)notification
{
```

```objectivec
NSDictionary *hwInfo = [notification userInfo]; // 硬件问题的详细信息
NSMutableDictionary *adminInfo = nil;

// 如果硬件警报严重到需要通知管理员，
// 则发布一条包含硬件故障和时间戳的新通知。
int alertLevel = [[hwInfo objectForKey:@"Level"] intValue];
if (alertLevel >= notifyAlertLevel) {
    // 创建硬件故障信息字典的可变副本
    adminInfo = [NSMutableDictionary dictionaryWithDictionary:hwInfo];
    // 向硬件信息字典添加时间戳
    [adminInfo setObject:[NSDate date] forKey:@"Date"];
    [[NSNotificationCenter defaultCenter] postNotificationName:@"AdminAlert"
                                                      object:[notification object]
                                                    userInfo:adminInfo];
}
```

Objective-C 近期新增了一些集合类：`NSPointerArray`、`NSMapTable` 和 `NSHashTable`。这些类本身是可变的，在这方面更类似于 Java 的集合类。它们都可以通过特定的“个性”进行“编程”，例如维护对象的弱引用、允许将内存块或原始整数用作值，或者允许存储 `nil` 对象指针。这些新类与旧类之间的差异将在相关章节中详细讨论。

[www.it-ebooks.info](http://www.it-ebooks.info/)

