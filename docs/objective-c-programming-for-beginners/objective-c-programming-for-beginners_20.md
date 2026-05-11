# 类别与扩展

通过掌握用于封装行为的块以及使用 GCD 管理多线程任务，开发者能够在遵循性能和内存管理最佳实践的同时，构建响应迅速且可扩展的应用程序。理解这些概念对于在 Objective-C 应用中充分发挥多线程和异步编程的潜力至关重要。

## 类别

类别允许你向现有类添加方法，而无需为其创建子类。这对于扩展那些你无法控制源代码或希望保持不同关注点分离的类尤为有用。

### 示例 - 类别声明：

```objective-c
// NSString+CustomMethods.h
#import <Foundation/Foundation.h>

@interface NSString (CustomMethods)
- (NSString *)reverseString;
@end
```

### 示例 - 类别实现：

```objective-c
// NSString+CustomMethods.m
#import "NSString+CustomMethods.h"

@implementation NSString (CustomMethods)
- (NSString *)reverseString {
    NSMutableString *reversedString = [NSMutableString string];
    for (NSInteger i = self.length - 1; i >= 0; i--) {
        [reversedString appendFormat:@"%C", [self characterAtIndex:i]];
    }
    return reversedString;
}
@end
```

类别允许你在不创建子类的情况下，向内置或第三方类添加额外功能，从而使代码模块化且更易于维护。

## 扩展（类扩展）

扩展与类别类似，但允许你在类的主 `@interface` 块内声明额外的方法、属性或实例变量。它们通常用于声明仅在类实现内部可访问的私有方法或属性。

### 示例 - 扩展声明：

```objective-c
// MyClass.h
#import <Foundation/Foundation.h>

@interface MyClass : NSObject
// 公共方法和属性
@end

// MyClass.m
#import "MyClass.h"

@interface MyClass ()
@property (nonatomic, strong) NSString *privateProperty;
- (void)privateMethod;
@end

@implementation MyClass
// 公共方法和私有方法的实现
@end
```

扩展有助于组织类的实现细节、声明私有 API，并通过将相关代码聚合在一起来提升代码可读性。

## 键值观察（KVO）

键值观察（KVO）允许对象观察其他对象属性的变化。当被观察的属性发生改变时，观察者对象会收到通知，从而能够执行相应的操作。

### 示例 - 设置 KVO：

```objective-c
// 定义一个包含待观察属性的类
@interface MyClass : NSObject
@property (nonatomic, assign) NSInteger value;
@end

// 在另一个类中实现 KVO
@implementation ObserverClass
- (void)observeValueForKeyPath:(NSString *)keyPath
                     ofObject:(id)object
                       change:(NSDictionary<NSKeyValueChangeKey, id> *)change
                      context:(void *)context {
    if ([keyPath isEqualToString:@"value"]) {
        NSLog(@"值已更改: %@", change[NSKeyValueChangeNewKey]);
    }
}

// 设置 KVO
MyClass *myObject = [[MyClass alloc] init];
[myObject addObserver:self forKeyPath:@"value" options:NSKeyValueObservingOptionNew context:nil];

// 更新值（将触发观察者方法）
myObject.value = 10;

// 完成时移除观察者
[myObject removeObserver:self forKeyPath:@"value"];
@end
```

KVO 用于观察并响应对象属性的变化，有助于实现松耦合的设计以及用户界面的动态更新。

## 动态类型与动态绑定

### 动态类型

Objective-C 是动态类型语言，这意味着对象的类型是在运行时而非编译时确定的。这允许将不同类型的对象灵活地赋值给变量。

#### 示例 - 动态类型：

```objective-c
id dynamicObject;
// 将不同类型的对象赋值给 dynamicObject
dynamicObject = @"Hello"; // NSString
dynamicObject = @123;     // NSNumber
dynamicObject = [NSDate date]; // NSDate
```

动态类型允许容器（`NSArray`、`NSDictionary`）容纳各种类型的对象，并支持通用编程结构。

### 动态绑定

Objective-C 中的动态绑定是指一种运行时机制，方法调用根据接收者实际的类或子类在运行时解析。这实现了对象之间的多态性和消息传递。

#### 示例 - 动态绑定：

```objective-c
// 基类
@interface Animal : NSObject
- (void)speak;
@end

@implementation Animal
- (void)speak {
    NSLog(@"动物在说话");
}
@end

// 子类
@interface Dog : Animal
@end

@implementation Dog
- (void)speak {
    NSLog(@"狗在叫");
}
@end

// 动态绑定的使用
Animal *animal = [[Dog alloc] init];
[animal speak]; // 输出: 狗在叫（根据实际对象类型在运行时解析）
```

动态绑定支持多态性，并使对象能够根据运行时的实际类对方法调用做出不同响应，从而增强了代码的灵活性和面向对象的设计原则。

理解类别和扩展可以让你在 Objective-C 中扩展和增强现有类。



