# 第 6 章：内存管理

Objective-C 中的内存分配涉及管理内存，以动态存储对象和数据结构。这对于高效使用内存以及防止内存泄漏或崩溃至关重要。



Objective-C 提供了两种主要的内存管理方式：手动引用计数（`MRC`）和自动引用计数（`ARC`）。

## 内存分配基础

局部变量和函数调用信息存储在此处。函数内部声明的变量在栈上分配。动态分配的对象和结构体的内存在此处管理。使用 `alloc`、`init`、`new` 或 `copy` 方法创建的对象在堆上分配。

## 手动引用计数（`MRC`）

手动引用计数（`MRC`）要求开发者通过使用 `retain`、`release`、`autorelease` 和 `dealloc` 方法来增加和减少对象的引用计数，从而显式地管理对象的生命周期。

**示例 -** `MRC`**:**

```objective
// MRC 示例
@interface Person : NSObject
@property (nonatomic, strong) NSString *name;
@end

@implementation Person
- (void)dealloc {
    [_name release]; // 释放已持有的属性
    [super dealloc]; // 调用父类的 dealloc 方法
}
@end

// 使用示例
Person *person = [[Person alloc] init]; // 分配内存
person.name = @"Alice"; // 赋值属性
[person release]; // 使用完对象后释放内存
```

在 `MRC` 中，开发者负责管理对象的生命周期，确保在不再需要对象时释放它们以回收内存。

## 自动引用计数（`ARC`）

自动引用计数（`ARC`）是 Objective-C 中引入的一项编译器特性，它可以自动管理 Objective-C 对象的生命周期。它在编译时插入 `retain`、`release` 和 `autorelease` 调用，从而减少内存泄漏和崩溃的可能性。

**示例 -** `ARC`**:**

```objective
// ARC 示例
@interface Person : NSObject
@property (nonatomic, strong) NSString *name;
@end

@implementation Person
@end

// 使用示例
Person *person = [[Person alloc] init]; // 内存自动管理
person.name = @"Bob"; // 无需手动管理 retain/release
// 当对象不再被引用时，ARC 会处理内存释放
```

在 `ARC` 中，开发者不再需要手动插入 `retain`/`release` 调用。编译器会根据对象的作用域和所有权，自动插入这些调用来管理内存。

## 主要差异与考量

在 `MRC` 中，开发者需要显式声明所有权并手动管理引用计数。在 `ARC` 中，所有权由编译器根据变量作用域和注解（`strong`、`weak`、`copy` 等）推断得出。`ARC` 的便利性减少了样板代码，简化了内存管理，相比 `MRC` 使代码更简洁且不易出错。`ARC` 向后兼容现有的 `MRC` 代码，允许逐步采用并迁移至 `ARC`。

理解内存分配、手动引用计数（`MRC`）和自动引用计数（`ARC`）对于开发健壮且高效的 Objective-C 应用至关重要。

`MRC` 要求显式管理引用计数和内存释放，而 `ARC` 在编译时自动执行这些任务，减少了开发者的负担并提高了代码安全性。在 `MRC` 和 `ARC` 之间进行选择取决于项目需求、遗留代码的兼容性以及开发者对手动与自动内存管理的偏好。

## 循环引用与弱引用

当两个或多个对象相互持有强引用时，就会发生循环引用，导致它们无法被释放。这可能会引发内存泄漏，因为每个对象的引用计数永远不会归零，即使它们已经不再被需要。

**示例 - 循环引用：**

```objective
@interface Person : NSObject
@property (nonatomic, strong) NSString *name;
@property (nonatomic, strong) Person *friend; // 强引用
@end

@implementation Person
@end

// 使用示例
Person *person1 = [[Person alloc] init];
Person *person2 = [[Person alloc] init];
person1.friend = person2;
person2.friend = person1;
```

在这个示例中，`person1` 和 `person2` 通过 `friend` 属性相互持有强引用，形成了一个循环引用。结果，两个对象都无法被释放，因为它们的引用计数永远不会降至零。

## 弱引用

为了打破循环引用，可以使用弱引用。弱引用不会持有它所指向的对象，允许在不存在其他强引用时释放被引用的对象。弱引用通常用于父子关系或委托模式中。

**示例 - 弱引用：**

```objective
@interface Person : NSObject
@property (nonatomic, strong) NSString *name;
@property (nonatomic, weak) Person *friend; // 弱引用
@end

@implementation Person
@end

// 使用示例
Person *person1 = [[Person alloc] init];
Person *person2 = [[Person alloc] init];
person1.friend = person2;
person2.friend = person1; // 使用弱引用来避免循环引用
```

在这个修正后的示例中，`Person` 类的 `friend` 属性被声明为 `weak`。这打破了 `person1` 和 `person2` 之间的循环引用，使得当它们的强引用被释放时，对象能够被正确地回收。

## Objective-C 内存管理最佳实践

### 1. 尽可能使用 `ARC`

自动引用计数（`ARC`）通过在编译时自动管理引用计数，减少了手动错误，并简化了内存管理。

### 2. 利用 `Strong`、`Weak` 和 `Unowned` 引用明确所有权

对于所有权关系（即只要存在强引用，对象就不应被释放），使用 `strong` 引用。使用 `weak` 引用来打破循环引用，避免对象间的强引用环。



