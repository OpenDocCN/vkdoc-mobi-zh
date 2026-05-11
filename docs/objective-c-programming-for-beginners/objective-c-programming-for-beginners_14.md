# 第五章 面向对象编程概念

面向对象编程（OOP）是一种编程范式，它将软件设计围绕对象而非函数和逻辑进行组织。它强调以下原则：

- **封装**：将数据（属性）和操作数据的方法（函数）捆绑到单个单元（对象）中。
- **抽象**：通过以适当的细节层次对类和对象进行建模，简化复杂系统。
- **继承**：允许类从其他类继承属性和方法。
- **多态**：提供一种方式，根据调用它的对象，以不同的方式执行同一动作。

## Objective-C 中的类和对象

### 1. 类

在 Objective-C 中，类是创建对象的蓝图或模板。它定义了该类的所有对象将拥有的属性（属性）和方法（函数）。

#### 示例 - 声明一个类：

```objectivec
// 接口部分（头文件 .h）
@interface Car : NSObject

// 属性（属性）
@property (nonatomic, strong) NSString *model;
@property (nonatomic) int year;

// 方法（函数）
- (void)startEngine;
- (void)drive;

@end
```

在此示例中，`Car` 是一个继承自 `NSObject` 的类。它声明了两个属性（`model` 和 `year`）和两个方法（`startEngine` 和 `drive`）。

### 2. 对象

对象是类的实例。它们表示由类定义的特定数据和行为实例。

#### 示例 - 创建和使用对象：

```objectivec
// 实现部分（实现文件 .m）
@implementation Car

- (void)startEngine {
    NSLog(@"Engine started.");
}

- (void)drive {
    NSLog(@"Car is driving.");
}

@end

// 主程序
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // 创建 Car 类的对象
        Car *myCar = [[Car alloc] init];
        
        // 使用对象属性和方法
        myCar.model = @"Toyota";
        myCar.year = 2020;
        NSLog(@"Car model: %@, year: %d", myCar.model, myCar.year);
        
        [myCar startEngine]; // 输出: Engine started.
        [myCar drive];       // 输出: Car is driving.
    }
    return 0;
}
```

在此示例中，`myCar` 是 `Car` 类的一个对象。设置了属性（`model` 和 `year`），并在 `myCar` 上调用了方法（`startEngine` 和 `drive`）。

## Objective-C 中的继承和多态

### 1. 继承

继承允许一个类（子类）继承另一个类（父类）的属性和方法。它促进了代码的重用和类的层次化组织。

#### 示例 - 继承：

```objectivec
// 基类: Vehicle
@interface Vehicle : NSObject
@property (nonatomic, strong) NSString *brand;
- (void)start;
@end

@implementation Vehicle
- (void)start {
    NSLog(@"Vehicle started.");
}
@end

// 子类: Car (继承自 Vehicle)
@interface Car : Vehicle
@property (nonatomic, strong) NSString *model;
- (void)drive;
@end

@implementation Car
- (void)drive {
    NSLog(@"Car is driving.");
}
@end
```

在此示例中，`Vehicle` 是一个基类，具有 `brand` 属性和 `start` 方法。`Car` 继承自 `Vehicle`，并添加了自己的属性（`model`）和方法（`drive`）。

### 2. 多态

多态允许不同类的对象被视为公共父类的对象。它使得基于实际调用对象的方法调用具有灵活性。


## 示例 - 多态

```objective
// 如上定义的 Vehicle 和 Car 类的接口
// 演示多态的函数
void driveAnyVehicle(Vehicle *vehicle) {
    [vehicle drive]; // 根据实际对象类型（Vehicle 或 Car）调用 drive 方法
}

// 主程序
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Vehicle *myVehicle = [[Vehicle alloc] init];
        Car *myCar = [[Car alloc] init];
        driveAnyVehicle(myVehicle); // 输出：Vehicle started.
        driveAnyVehicle(myCar);     // 输出：Car is driving.
    }
    return 0;
}
```

在此示例中，`driveAnyVehicle` 函数将 `Vehicle` 对象作为参数接收。由于多态性，它可以接受 `Vehicle` 和 `Car` 对象，并根据实际对象类型调用相应的 `drive` 方法。

Objective-C 中的面向对象编程（OOP）围绕类和对象、封装、继承以及多态展开。类定义了对象的结构和行为，而对象是类的实例，它们通过方法和属性相互交互。理解这些概念对于在 Objective-C 中构建模块化、可扩展且可维护的软件系统至关重要。

## 封装与抽象

封装是将数据（属性）和操作数据的函数（方法）捆绑到单个单元（对象）中的过程。它允许隐藏数据，仅暴露必要的接口来与对象交互。在 Objective-C 中，封装通过类和访问修饰符来实现。

### 示例 - 封装

```objective
// 接口部分（头文件 .h）
@interface Car : NSObject
// 私有属性（已封装）
@property (nonatomic, strong) NSString *model;
@property (nonatomic) int year;
// 公开方法（与对象交互的接口）
- (void)startEngine;
- (void)drive;
@end

// 实现部分（实现文件 .m）
@implementation Car
- (void)startEngine {
    NSLog(@"Engine started.");
}
- (void)drive {
    NSLog(@"Car is driving.");
}
@end
```

在此示例中，`Car` 将属性（`model` 和 `year`）和方法（`startEngine` 和 `drive`）封装在单个单元中。实现细节对外部代码隐藏，仅公开接口（方法）可访问。

### 抽象

抽象是指通过在适当的细节层次上建模类和对象，来简化复杂系统的过程。它关注本质特征，忽略无关细节。在 Objective-C 中，抽象通过类接口和方法声明来实现。

#### 示例 - 抽象

```objective
// 接口部分（头文件 .h）
@interface Shape : NSObject
- (void)draw; // 抽象方法声明
@end

// 实现部分（实现文件 .m）
@implementation Shape
- (void)draw {
    // 抽象方法实现因子类而异
    NSLog(@"Abstract method implementation");
}
@end
```

在此示例中，`Shape` 是一个抽象类，包含抽象方法 `draw`。`Shape` 的子类（例如 `Circle`、`Rectangle`）为每种形状提供了具体的 `draw` 实现。

## Objective-C 中的分类与协议

### 分类

分类允许你向现有类添加方法，而无需对其创建子类。它们提供了一种扩展类功能的方式，特别适用于向内置类或第三方库添加方法。

#### 示例 - 分类

```objective
// 分类接口（头文件 .h）
@interface NSString (CustomString)
- (NSString *)reverse;
@end

// 分类实现（实现文件 .m）
@implementation NSString (CustomString)
- (NSString *)reverse {
    NSMutableString *reversedString = [NSMutableString stringWithCapacity:[self length]];
    for (NSInteger i=[self length]-1; i>=0; i--) {
        [reversedString appendString:[NSString stringWithFormat:@"%c", [self characterAtIndex:i]]];
    }
    return reversedString;
}
@end
```

在此示例中，`NSString` 通过分类 `CustomString` 进行了扩展，添加了一个用于反转字符串的 `reverse` 方法。

### 协议

协议声明了任何类都可以实现的方法。它们定义了类必须遵循的方法蓝图。协议提供了一种实现多态性的方式，并支持不同继承层次结构中的类之间进行通信。

#### 示例 - 协议

```objective
// 协议声明（头文件 .h）
@protocol PrinterProtocol
- (void)print;
@end

// 采用协议的类
@interface Printer : NSObject
@end

// 协议实现（实现文件 .m）
@implementation Printer
- (void)print {
    NSLog(@"Printing...");
}
@end
```

在此示例中，`PrinterProtocol` 定义了一个 `print` 方法，任何采用该协议的类都必须实现它。`Printer` 采用了 `PrinterProtocol`，并提供了 `print` 方法的实现。

## 结论

封装和抽象是面向对象编程（OOP）中的基本原则，它们促进了代码的模块化、可维护性和可重用性。Objective-C 中的分类和协议提供了扩展和增强类功能、以及定义支持对象间通信的接口的机制。理解并应用这些概念和机制，能让开发者编写出高效、可扩展且结构良好的 Objective-C 代码。

---

