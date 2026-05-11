# 在 Objective-C 中使用 Block 进行枚举和排序

## 使用 Block 进行枚举

从表面上看，使用 Block 枚举集合类与使用之前的方法相比，似乎并没有明显更优或更简便。`NSDictionary` 对应的 `enumerateKeysAndObjectsUsingBlock:` 方法相当不错，因为它将键和对象都传递给 Block，但除此之外，这些方法并无太大差别。然而，Block 方法的优势在于其扩展版本 `enumerateObjectsWithOptions:usingBlock:`。

第一个参数是一个选项位掩码，其中最主要的是 `NSEnumerationConcurrent`。这告诉数组并发枚举其元素，在多核处理器设备上能带来巨大收益（尽管在某些情况下也能显著提升单核设备的性能）。以下代码将以并发方式对 `myArray` 中的每个对象执行 `performSomeTask`：

```objc
[myArray enumerateObjectsWithOptions:NSEnumerationConcurrent
                          usingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
                              [obj performSomeTask];
                          }];
```

就这样，你可以利用 iOS 先进的多线程性能，而无需编写一行线程代码。该方法会等到所有对象枚举完毕才返回，但它会尽可能多地并发运行枚举实例。随着越来越多的设备拥有多核，这种枚举方法应成为你的默认选择，因为它是一种无需增加开发成本就能提升应用性能的简便方式。我们将在下一章更详细地讨论多核处理，但这是一个良好的开端。目前，你可以将其作为提升应用在枚举集合类时性能的工具。以上就是 Block 为何适合枚举的简要介绍。Block 还能帮助解决的另一个密切相关的问题是数组排序。

### 使用 Block 对数组排序

与枚举类似，数组排序是许多编程语言中的常见任务。基本概念在各语言中相似：根据排序类型，以某种模式遍历数组，比较值并执行相应操作。使用哪种排序类型存在广泛争议，但对值进行排序几乎是每种编程语言都需要处理的问题。Objective-C 也不例外，它提供了几种对数组排序的方法。

### 使用比较选择器对数组排序

对数组进行排序最直接的方法是对每个对象使用比较方法。这通过 `NSArray` 的实例方法 `sortedArrayUsingSelector:` 实现，该方法接受一个比较选择器作为参数，并返回一个新的 `NSArray`，通过将选择器发送给数组中每个对象（另一个对象作为参数）来完成排序。要对字符串数组进行不区分大小写的排序，代码如下：

```objc
NSArray *sortedArray = [myArray sortedArrayUsingSelector:@selector(caseInsensitiveCompare:)];
```

数组中的每个对象都会收到 `caseInsensitiveCompare:` 消息，并以另一个对象作为参数。这种方法相当直接，但不够灵活；它仅能基于一个方法对数组排序，并且为了正常工作，数组中的每个对象都必须实现相同的方法。

还有一类类似的排序方法，例如 `sortedArrayUsingFunction:context:`，它使用 C 函数对数组排序。虽然它们不依赖成员对象实现任何功能，但比较过程的执行路径仍然受限。如果你希望基于多个条件排序，最好使用排序描述符。

### 使用排序描述符对数组排序

排序描述符是一种封装了数据排序方式的对象。单个描述符聚焦于每个对象的一个属性，并根据该属性进行排序。使用排序描述符时，你需要一个描述符数组来构建排序层次结构，用于对对象进行排序。

给定一个名为 `Person` 的 Objective-C 类，包含 `name` 和 `age` 属性，以下代码先按年龄从大到小排序，再按字母顺序对姓名排序：

```objc
NSSortDescriptor *ageDescriptor = [[NSSortDescriptor alloc] initWithKey:@"age"
                                                              ascending:NO];
NSSortDescriptor *nameDescriptor = [[NSSortDescriptor alloc] initWithKey:@"name"
                                                               ascending:YES];
NSArray *sortDescriptors = [NSArray arrayWithObjects:ageDescriptor,
                                                     nameDescriptor, nil];
NSArray *sortedArray = [myArray sortedArrayUsingDescriptors:sortDescriptors];
```

默认情况下，排序描述符会使用 `compare:` 方法来比较两个项目，但在创建时也可以修改使用的选择器。如果你想用 `localizedCaseInsensitiveCompare:` 方法对字符串数组排序，可以这样做：

```objc
NSSortDescriptor *sortDescriptor =
    [NSSortDescriptor sortDescriptorWithKey:nil
                                  ascending:YES
                                   selector:@selector(localizedCaseInsensitiveCompare:)];
NSArray *sortDescriptors = [NSArray arrayWithObject:sortDescriptor];
NSArray *sortedArray = [myArray sortedArrayUsingDescriptors:sortDescriptors];
```

当数据中有大量相同值，需要第二、第三甚至更多排序方法时，排序描述符非常有用。当引入 Block 后，它们变得更加实用。

### 使用 Block 对数组排序

使用 Block 对数组排序时，通常会使用一种名为 `NSComparator` 的已定义 Block 类型。`NSComparator` 在系统头文件中的定义如下：

```objc
typedef NSComparisonResult (^NSComparator)(id obj1, id obj2);
```

该 Block 返回一个 `NSComparisonResult`，并接受两个参数，分别对应待比较的对象。有三种可能的返回值：当 `obj1` 小于 `obj2` 时返回 `NSOrderedAscending`；当 `obj1` 大于 `obj2` 时返回 `NSOrderedDescending`；相等时返回 `NSOrderedSame`。Block 的实现可以随心所欲地使用 `obj1` 和 `obj2`，使用 `NSComparator` 对数组排序也非常简单：

```objc
NSArray *sortedArray = [myArray sortedArrayUsingComparator:^NSComparisonResult(id obj1, id obj2) {
    return [obj1 caseInsensitiveCompare:obj2];
}];
```

在这个例子中，我们只是简单地调用了 `caseInsensitiveCompare:` 方法，但你可以使用任何适合比较值的逻辑。你也可以通过比较器 Block 来创建 `NSSortDescriptor` 对象：

```objc
NSSortDescriptor *nameSortDescriptor =
    [[NSSortDescriptor alloc] initWithKey:@"name"
                                ascending:YES
                               comparator:^NSComparisonResult(id obj1, id obj2) {
                                   return [obj1 caseInsensitiveCompare:obj2];
                               }];
NSArray *sortDescriptors = [NSArray arrayWithObject:nameSortDescriptor];
NSArray *sortedArray = [myArray sortedArrayUsingDescriptors:sortDescriptors];
```

这里的 Block 相同，但我们可以将其与 `NSSortDescriptor` 结合，利用其支持多个排序标准的能力。

与集合枚举类似，使用 Block 时数组排序也拥有一个秘密武器。我们可以通过 `sortedArrayWithOptions:usingComparator:` 方法自动对数组进行并发排序。以下代码对字符串数组进行并发排序：

```objc
NSArray *sortedArray = [myArray sortedArrayWithOptions:NSSortConcurrent
                                       usingComparator:^NSComparisonResult(id obj1, id obj2) {
                                           return [obj1 caseInsensitiveCompare:obj2];
                                       }];
```



