# 13. For-Each 循环

**摘要**

`For-each` 循环是一种更具体的循环类型，只能用于 `NSArray` 和 `NSDictionary` 等集合对象。当你需要遍历对象列表并对列表中的每个对象执行操作时，可以使用 `for` 循环。

## For-Each 循环的定义

`For-each` 循环是一种更具体的循环类型，只能用于 `NSArray` 和 `NSDictionary` 等集合对象。当你需要遍历对象列表并对列表中的每个对象执行操作时，可以使用 `for` 循环。

例如，以第 8 章中的数组为例：

```
NSArray *numbers = @[@-2, @-1, @0, @1, @2];
```

在第 8 章中，你使用了带块的枚举方法遍历列表并对每个数字求平方。你可以使用 `for` 循环作为枚举方法的替代方案。

```
for (NSNumber *num in numbers){
    NSLog(@"num ^ 2= %f", [num floatValue] * [num floatValue]);
}
```

该循环以 `for` 关键字开头，后面是括号内的规范。规范以对象类型（`NSNumber`）和一个变量（`*num`）开头，该变量将提供对列表中当前对象的引用。

接下来，你可以看到关键字 `in`，后面跟着数组（`numbers`）。综合来看，你可以将其理解为“对于数组 `numbers` 中的每个数字 num，执行某些操作”。“某些操作”定义在循环第一部分后面的代码块中。它将遍历整个数组并对每个数字求平方，生成以下输出：

```
num ^ 2= 4.000000
num ^ 2= 1.000000
num ^ 2= 0.000000
num ^ 2= 1.000000
num ^ 2= 4.000000
```

### 使用 NSDictionary 的 For 循环

字典比数组稍微复杂一些，因为字典维护了一个键和对象的列表。你可能会认为对字典使用 `for` each 循环会得到对象列表；但实际上，你只会得到字典键的列表。

因此，如果你像下面这样对 `NSDictionary` 使用 `for` each 循环：

```
NSDictionary *d1 = @{@"one": @1, @"two": @2, @"three": @3};
for (id object in d1){
    NSLog(@"object = %@", object);
}
```

你将得到如下列出所有键的输出：

```
object = one
object = two
object = three
```

如果你想输出字典中的值，你需要向字典发送 `objectForKey:` 消息。

```
for (id object in d1){
    NSNumber *num = [d1 objectForKey:object];
    NSLog(@"num = %@", num);
}
```

这个 `for` 循环将打印出对象的值：

```
num = 1
num = 2
num = 3
```

