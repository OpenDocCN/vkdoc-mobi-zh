# 第 7 章：使用集合

## 数组

Objective-C 中的数组是对象的有序集合。它们可以容纳任何 Objective-C 类的多个对象，包括包装在 `NSNumber` 中的基本类型。数组分为不可变数组（`NSArray`）和可变数组（`NSMutableArray`）。

### 示例 - NSArray：

```objectivec
// 创建不可变数组
NSArray *fruits = @[@"Apple", @"Banana", @"Orange"];

// 访问元素
NSString *firstFruit = fruits[0]; // Apple
NSString *secondFruit = [fruits objectAtIndex:1]; // Banana

// 遍历元素
for (NSString *fruit in fruits) {
    NSLog(@"Fruit: %@", fruit);
}
```

### 示例 - NSMutableArray：

```objectivec
// 创建可变数组
NSMutableArray *mutableFruits = [NSMutableArray arrayWithObjects:@"Apple", @"Banana", @"Orange", nil];

// 修改元素
[mutableFruits addObject:@"Grapes"]; // 添加元素
[mutableFruits removeObjectAtIndex:1]; // 移除元素

// 遍历元素
for (NSString *fruit in mutableFruits) {
    NSLog(@"Fruit: %@", fruit);
}
```

`NSArray` 一旦初始化就是不可变的，而 `NSMutableArray` 允许添加、移除或替换元素。

## Objective-C 中的字典和 NSMutableDictionary

### 字典

Objective-C 中的字典是键值对的无序集合。键必须是唯一对象，而值可以是任何 Objective-C 类的对象，包括包装在 `NSNumber` 中的基本类型。

字典分为不可变字典（`NSDictionary`）和可变字典（`NSMutableDictionary`）。

#### 示例 - NSDictionary：

```objectivec
// 创建不可变字典
NSDictionary *person = @{@"name": @"John", @"age": @30};

// 访问值
NSString *name = person[@"name"]; // John
NSNumber *age = [person objectForKey:@"age"]; // 30

// 遍历键值对
for (NSString *key in person) {
    NSLog(@"Key: %@, Value: %@", key, person[key]);
}
```

#### 示例 - NSMutableDictionary：

```objectivec
// 创建可变字典
NSMutableDictionary *mutablePerson = [NSMutableDictionary dictionary];
[mutablePerson setObject:@"Alice" forKey:@"name"]; // 设置值
[mutablePerson setObject:@25 forKey:@"age"];

// 修改值
[mutablePerson setObject:@26 forKey:@"age"]; // 更新年龄

// 遍历键值对
for (NSString *key in mutablePerson) {
    NSLog(@"Key: %@, Value: %@", key, mutablePerson[key]);
}
```

`NSDictionary` 一旦初始化就是不可变的，而 `NSMutableDictionary` 允许添加、移除或修改键值对。

## 最佳实践：数组和字典

### 不可变与可变

- 当集合在初始化后不需要更改时，使用不可变版本（`NSArray`、`NSDictionary`），以确保数据完整性和线程安全。
- 可变集合（`NSMutableArray`、`NSMutableDictionary`）更灵活，但可能因动态调整大小而带来性能开销。

### 访问

- 使用下标语法（`array[index]`、`dictionary[key]`）以求简单和可读性。
- 使用快速枚举（`for-in` 循环）来遍历元素，既高效又简洁。

数组（`NSArray`、`NSMutableArray`）和字典（`NSDictionary`、`NSMutableDictionary`）是 Objective-C 中分别用于存储和管理对象集合及键值对的基本数据结构。理解何时使用不可变或可变版本、高效地访问元素以及使用快速枚举遍历集合，是在 Objective-C 应用中有效使用这些数据结构的关键方面。通过遵循最佳实践，开发者可以在处理数组和字典时确保高效的内存使用和优化的性能。

## 集合与 NSMutableSet

Objective-C 中的集合是唯一对象的无序集合，不包含重复元素。集合通过 `NSSet`（不可变）和 `NSMutableSet`（可变）实现。

### 示例 - NSSet：

```objectivec
// 创建不可变集合
NSSet *colors = [NSSet setWithObjects:@"Red", @"Green", @"Blue", nil];

// 检查成员关系
BOOL containsRed = [colors containsObject:@"Red"]; // YES

// 遍历元素
for (NSString *color in colors) {
    NSLog(@"Color: %@", color);
}
```

`NSSet` 确保元素的唯一性，且一旦初始化就是不可变的。



### 示例 - `NSMutableSet`

```objective-c
// 创建可变集合
NSMutableSet *mutableColors = [NSMutableSet setWithObjects:@"Red", @"Green", @"Blue", nil];
// 添加和移除元素
[mutableColors addObject:@"Yellow"]; // 添加元素
[mutableColors removeObject:@"Green"]; // 移除元素
// 遍历元素
for (NSString *color in mutableColors) {
    NSLog(@"Color: %@", color);
}
```

`NSMutableSet` 允许添加、移除或替换元素，并为集合提供可变行为。

### 在 Objective-C 中遍历集合

#### 快速枚举

`for-in` 循环（快速枚举）是在 Objective-C 中遍历集合（`NSArray`、`NSDictionary`、`NSSet` 等）的首选方法。它提供了高效且简洁的语法来依次访问每个元素。

**示例 - 快速枚举:**

```objective-c
NSArray *fruits = @[@"Apple", @"Banana", @"Orange"];
for (NSString *fruit in fruits) {
    NSLog(@"Fruit: %@", fruit);
}
```

快速枚举会遍历集合中的每个元素，无需显式的索引变量。

#### 使用 Block 进行枚举

Objective-C 集合（`NSArray`、`NSDictionary`、`NSSet`）支持使用 Block 进行枚举，以应对更复杂的迭代场景。Block 为对集合中的每个元素或键值对应用操作提供了灵活性。

**示例 - 使用 Block 进行枚举:**

```objective-c
NSArray *numbers = @[@1, @2, @3, @4];
[numbers enumerateObjectsUsingBlock:^(NSNumber *number, NSUInteger idx, BOOL *stop) {
    NSLog(@"Number at index %lu: %@", (unsigned long)idx, number);
}];
```

基于 Block 的枚举（`enumerateObjectsUsingBlock:`）允许对集合中的每个对象执行操作，同时访问对象及其索引。

## 最佳实践

#### 遍历集合

- **对于简单的遍历任务，使用快速枚举（for-in 循环）**，因为它们高效且易于阅读。
- **对于复杂逻辑，使用 Block**：当需要对每个元素或键值对执行具有更复杂逻辑的操作时，使用带有 Block 的枚举方法（`enumerateObjectsUsingBlock:`、`enumerateKeysAndObjectsUsingBlock:`）。

#### 不可变 vs. 可变

- 当集合在初始化后无需更改时，使用不可变集合（`NSSet`、`NSArray`、`NSDictionary`）以确保数据完整性和线程安全。

---

集合（`NSSet`、`NSMutableSet`）和迭代技术（`for-in` 循环、基于 Block 的枚举）是 Objective-C 中用于管理唯一对象集合并高效遍历它们的基本组件。理解不可变集合和可变集合之间的区别、针对不同场景选择合适的迭代方法以及遵循最佳实践，可以确保在 Objective-C 应用程序中有效使用集合并高效遍历集合。通过利用这些特性，开发人员可以在其 Objective-C 项目中处理集合时编写清晰、可读的代码并优化性能。

---

