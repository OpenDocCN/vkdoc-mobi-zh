# Objective-C 中的方法

它们封装了对象内部的行为与功能，使对象之间能够相互通信和交互。理解方法是掌握 Objective-C 面向对象编程的基础。

## 1. 定义方法

Objective-C 中的方法定义在类的 `@interface` 和 `@implementation` 代码块中。

### 实例方法

实例方法是作用于类实例（即对象）的方法。它们在类的 `@interface` 和 `@implementation` 部分进行声明和实现。

**示例 - 声明并实现一个实例方法：**

```
// 接口部分（头文件 .h）
@interface MyClass : NSObject
- (void)printMessage; // 实例方法声明
@end

// 实现部分（实现文件 .m）
@implementation MyClass
- (void)printMessage { // 实例方法定义
    NSLog(@"来自实例方法的问候！");
}
@end
```

### 类方法

类方法，在其他语言中也称为静态方法，是隶属于类本身而非类实例的方法。它们以 `+` 号作为前缀。

**示例 - 声明并实现一个类方法：**

```
// 接口部分（头文件 .h）
@interface MyClass : NSObject
+ (void)printStaticMessage; // 类方法声明
@end

// 实现部分（实现文件 .m）
@implementation MyClass
+ (void)printStaticMessage { // 类方法定义
    NSLog(@"来自类方法的问候！");
}
@end
```

## 2. 调用方法

方法通过方括号 `[]` 在对象上（针对实例方法）或直接在类上（针对类方法）进行调用。

**示例 - 调用实例方法和类方法：**

```
// 使用实例方法
MyClass *obj = [[MyClass alloc] init]; // 创建 MyClass 的一个实例
[obj printMessage]; // 在 obj 上调用实例方法

// 使用类方法
[MyClass printStaticMessage]; // 直接在 MyClass 上调用类方法
```

